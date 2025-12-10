# Agile Backlog

**Generated**: 2025-12-10T03:40:28.398634
**Total Epics**: 6
**Total Stories**: 18
**Total Points**: 135
**Estimation Scale**: fibonacci
**SSCS Compliance**: v2.0
**TDD Workflow**: RED → GREEN → REFACTOR
**Branch Naming**: `feature/{issue-number}-{slug}`


## Epic #1000: Authentication & User Management

Implement secure JWT-based authentication system with role-based access control for DJs and Admins

### User Stories

#### Story #1001: As a DJ, I want to register an account so that I can access the streaming platform

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement user registration endpoint with email validation, password hashing, and automatic DJ role assignment. Store user credentials securely in PostgreSQL with encrypted sensitive fields.

**Acceptance Criteria**:
1. Given a new user with valid email and password, When they submit registration form, Then account is created with DJ role and user_id is returned
2. Given a user with existing email, When they attempt to register, Then registration fails with 409 Conflict error
3. Given a user with invalid email format, When they submit registration, Then validation fails with 400 Bad Request
4. Given a user with weak password (< 8 chars, no special chars), When they register, Then validation fails with detailed password requirements

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for user registration endpoint`
- 🟢 GREEN: `green: user registration with JWT token generation`
- 🔵 REFACTOR: `refactor: extract password validation and email verification utilities`

**Branch**: `feature/1001-user-registration`

**Labels**: user-story, authentication, backend

#### Story #1002: As a DJ, I want to login with my credentials so that I can access my streaming dashboard

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement JWT-based authentication with access token (24h) and refresh token flow. Validate credentials against PostgreSQL and return signed tokens.

**Acceptance Criteria**:
1. Given a registered user with correct credentials, When they login, Then they receive access_token and refresh_token with 24h expiry
2. Given a user with incorrect password, When they attempt login, Then authentication fails with 401 Unauthorized
3. Given a user with non-existent email, When they attempt login, Then authentication fails with 401 Unauthorized
4. Given a valid refresh_token, When user requests token refresh, Then new access_token is issued with updated expiry

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for JWT authentication flow`
- 🟢 GREEN: `green: JWT login and refresh token endpoints`
- 🔵 REFACTOR: `refactor: extract JWT signing and verification middleware`

**Branch**: `feature/1002-jwt-authentication`

**Labels**: user-story, authentication, security

#### Story #1003: As an Admin, I want role-based access control so that I can manage DJ permissions securely

**Points**: 8 | **Type**: feature | **Status**: unstarted

Implement RBAC middleware with scope validation (streams:read, streams:write, playlists:write, etc.) and enforce resource ownership checks.

**Acceptance Criteria**:
1. Given a DJ with streams:write scope, When they create a stream, Then operation succeeds and stream is linked to their user_id
2. Given a DJ attempting to modify another DJ's stream, When they send PATCH request, Then operation fails with 403 Forbidden
3. Given an Admin with global access, When they query any DJ's resources, Then operation succeeds with full visibility
4. Given a request without required scope, When endpoint is accessed, Then middleware returns 403 Forbidden with missing scope details

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for RBAC middleware and scope validation`
- 🟢 GREEN: `green: RBAC middleware with scope enforcement`
- 🔵 REFACTOR: `refactor: extract permission checking utilities and improve error messages`

**Branch**: `feature/1003-rbac-middleware`

**Labels**: user-story, authorization, security


## Epic #2000: Live Streaming Relay (Shoutcast Integration)

Implement real-time Shoutcast source ingestion with FFmpeg-based HLS segmentation and multi-listener relay infrastructure

### User Stories

#### Story #2001: As a DJ, I want to register my Shoutcast source so that the platform can relay my live stream

**Points**: 8 | **Type**: feature | **Status**: unstarted

Create endpoint to register Shoutcast URL, mount point, and stream key. Validate connectivity to Shoutcast admin endpoint and spin up Kubernetes Relay Worker Pod.

**Acceptance Criteria**:
1. Given a DJ with valid Shoutcast credentials, When they register a stream, Then Relay Worker Pod is created and connects to Shoutcast source
2. Given an unreachable Shoutcast URL, When DJ attempts registration, Then validation fails with 422 Unprocessable Entity and connection error details
3. Given a DJ with monetization_enabled: true, When stream is registered, Then paywall configuration is applied to Relay Worker
4. Given a registered stream, When DJ queries stream status, Then current_track, bitrate, and listener count are returned from Shoutcast admin API

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for Shoutcast source registration and validation`
- 🟢 GREEN: `green: Shoutcast registration endpoint with Kubernetes Pod orchestration`
- 🔵 REFACTOR: `refactor: extract Shoutcast connectivity validator and Pod creation logic`

**Branch**: `feature/2001-shoutcast-registration`

**Labels**: user-story, live-streaming, kubernetes

#### Story #2002: As a listener, I want to access live HLS streams so that I can play audio in my browser or mobile app

**Points**: 13 | **Type**: feature | **Status**: unstarted

Implement Relay Worker that uses FFmpeg to segment Shoutcast feed into HLS TS chunks (6s duration) and serve live.m3u8 manifest via CDN.

**Acceptance Criteria**:
1. Given a registered live stream, When listener requests /streams/{stream_id}/live.m3u8, Then HLS manifest is served with 3 recent TS segments
2. Given a public stream, When unauthenticated listener accesses HLS endpoint, Then manifest is served without authentication
3. Given a private stream with monetization, When listener without subscription accesses HLS, Then 403 Forbidden is returned
4. Given an active Relay Worker, When Shoutcast source disconnects, Then Worker retries connection with exponential backoff and marks stream offline after 5 minutes

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for HLS manifest generation and CDN serving`
- 🟢 GREEN: `green: FFmpeg-based HLS segmentation in Relay Worker Pod`
- 🔵 REFACTOR: `refactor: extract FFmpeg command builder and segment cleanup logic`

**Branch**: `feature/2002-hls-relay-worker`

**Labels**: user-story, live-streaming, ffmpeg, critical

#### Story #2003: As a DJ, I want real-time listener analytics so that I can monitor my stream's performance

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement heartbeat mechanism where Relay Workers POST listener count every 30s, and expose analytics endpoint with daily aggregates.

**Acceptance Criteria**:
1. Given an active Relay Worker, When 30 seconds elapse, Then Worker POSTs heartbeat with listener_count and cpu_usage to API
2. Given a stream with historical data, When DJ queries /analytics/streams/{stream_id}?from=2025-06-01&to=2025-06-07, Then daily listener counts and peak_listeners are returned
3. Given a Relay Worker that stops sending heartbeats, When 2 minutes pass, Then Kubernetes restarts Pod and alert is sent to DJ
4. Given multiple concurrent listeners, When analytics endpoint is queried, Then accurate listener count is returned from Relay Worker state

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for Relay Worker heartbeat and analytics aggregation`
- 🟢 GREEN: `green: heartbeat endpoint and analytics query with PostgreSQL aggregation`
- 🔵 REFACTOR: `refactor: extract analytics aggregation queries and add caching layer`

**Branch**: `feature/2003-stream-analytics`

**Labels**: user-story, analytics, monitoring


## Epic #3000: Audio File Upload & On-Demand Playlists

Enable DJs to upload MP3 files with automatic HLS transcoding and create on-demand playlists with continuous streaming support

### User Stories

#### Story #3001: As a DJ, I want to upload MP3 files so that I can use them in playlists and podcasts

**Points**: 8 | **Type**: feature | **Status**: unstarted

Implement multipart file upload endpoint with virus scanning, S3 storage, and background FFmpeg transcoding to HLS segments.

**Acceptance Criteria**:
1. Given a DJ with valid MP3 file (<100MB), When they upload via /uploads/audio, Then file is stored in S3 and transcoding job is enqueued
2. Given an uploaded file, When transcoding completes, Then HLS manifest and TS segments are generated in S3 under /uploads/audio/{upload_id}/hls/
3. Given a file upload in progress, When DJ queries /uploads/audio/{upload_id}, Then status is 'processing' with progress percentage
4. Given a file larger than 100MB, When upload is attempted, Then 413 Payload Too Large is returned with size limit details

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for multipart file upload and S3 storage`
- 🟢 GREEN: `green: file upload endpoint with S3 integration and transcoding queue`
- 🔵 REFACTOR: `refactor: extract file validation, virus scanning, and transcoding job creation`

**Branch**: `feature/3001-audio-file-upload`

**Labels**: user-story, file-upload, backend

#### Story #3002: As a DJ, I want to create on-demand playlists so that listeners can stream my curated tracks

**Points**: 8 | **Type**: feature | **Status**: unstarted

Implement playlist creation endpoint supporting both uploaded files (via upload_id) and external URLs, with automatic HLS manifest generation.

**Acceptance Criteria**:
1. Given a DJ with uploaded tracks, When they create playlist with upload_ids, Then playlist is created and HLS manifest is pre-generated
2. Given a playlist with external URLs, When created, Then API validates URLs with HEAD requests and stores references
3. Given a playlist with mixed sources (uploads + external), When listener requests /playlists/{id}/stream.m3u8, Then unified HLS manifest is served
4. Given a private playlist with monetization, When unauthenticated listener accesses, Then 403 Forbidden is returned with subscription requirement

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for playlist creation and HLS manifest generation`
- 🟢 GREEN: `green: playlist CRUD endpoints with HLS manifest builder`
- 🔵 REFACTOR: `refactor: extract URL validator and manifest generation utilities`

**Branch**: `feature/3002-on-demand-playlists`

**Labels**: user-story, playlists, backend

#### Story #3003: As a listener, I want continuous MP3 streaming for playlists so that I can listen without interruptions

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement /playlists/{id}/stream.mp3 endpoint that concatenates MP3 files and streams with chunked transfer encoding.

**Acceptance Criteria**:
1. Given a playlist with 3 tracks, When listener requests /stream.mp3, Then MP3 data is streamed continuously with chunked encoding
2. Given a playlist with external URLs, When streaming, Then API proxies MP3 data from external sources seamlessly
3. Given a listener mid-stream, When they disconnect and reconnect, Then streaming resumes from last buffered position
4. Given a public playlist, When 1000 concurrent listeners connect, Then CDN caching reduces origin load to <10 requests/sec

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for continuous MP3 streaming and chunked encoding`
- 🟢 GREEN: `green: MP3 streaming endpoint with chunked transfer`
- 🔵 REFACTOR: `refactor: extract MP3 concatenation logic and add CDN cache headers`

**Branch**: `feature/3003-continuous-mp3-streaming`

**Labels**: user-story, streaming, performance


## Epic #4000: Podcast Hosting & Custom Domains

Enable DJs to create podcast series with custom domains, automated DNS/TLS provisioning, and RSS 2.0 feed generation

### User Stories

#### Story #4001: As a DJ, I want to create a podcast series so that I can publish episodes with custom branding

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement podcast creation endpoint with metadata storage and automatic RSS feed generation at /podcasts/{id}/rss.xml.

**Acceptance Criteria**:
1. Given a DJ with podcast metadata (title, description, author), When they create podcast, Then podcast_id is returned and RSS feed is initialized
2. Given a podcast with episodes, When RSS feed is requested, Then valid RSS 2.0 XML is served with <channel> and <item> entries
3. Given a podcast with explicit content, When RSS is generated, Then <itunes:explicit>yes</itunes:explicit> tag is included
4. Given a podcast with monetization, When listener accesses RSS without subscription, Then <enclosure> URLs are signed with expiry

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for podcast creation and RSS generation`
- 🟢 GREEN: `green: podcast CRUD endpoints with RSS XML builder`
- 🔵 REFACTOR: `refactor: extract RSS template engine and XML validation`

**Branch**: `feature/4001-podcast-creation`

**Labels**: user-story, podcasts, backend

#### Story #4002: As a DJ, I want to register a custom domain for my podcast so that I can use my own branding

**Points**: 13 | **Type**: feature | **Status**: unstarted

Implement custom domain registration with DNS TXT verification, CNAME creation, and automated Let's Encrypt TLS certificate provisioning.

**Acceptance Criteria**:
1. Given a DJ with domain ownership, When they register podcast.djname.com, Then DNS TXT record value is returned for verification
2. Given a verified domain, When DNS TXT record is detected, Then CNAME is created pointing to CDN and TLS certificate is requested
3. Given a domain with issued TLS, When listener accesses https://podcast.djname.com/rss.xml, Then RSS feed is served over HTTPS
4. Given a domain verification failure, When 24 hours pass, Then DJ receives email alert with troubleshooting steps

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for custom domain registration and DNS verification`
- 🟢 GREEN: `green: domain registration endpoint with Route53 and Let's Encrypt integration`
- 🔵 REFACTOR: `refactor: extract DNS verification poller and TLS provisioning workflow`

**Branch**: `feature/4002-custom-domains`

**Labels**: user-story, podcasts, dns, critical

#### Story #4003: As a DJ, I want to add episodes to my podcast so that listeners can download and subscribe

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement episode creation endpoint supporting uploaded files and external URLs, with automatic RSS feed regeneration.

**Acceptance Criteria**:
1. Given a podcast with existing episodes, When DJ adds new episode, Then RSS feed is regenerated with new <item> entry
2. Given an episode with upload_id, When added, Then <enclosure> URL points to S3 object with correct length and type
3. Given an episode with external media_url, When added, Then API validates URL and stores reference without re-hosting
4. Given a paywalled episode, When listener without subscription accesses <enclosure>, Then signed URL expires after 1 hour

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for episode creation and RSS regeneration`
- 🟢 GREEN: `green: episode CRUD endpoints with RSS update trigger`
- 🔵 REFACTOR: `refactor: extract RSS regeneration job and CDN invalidation logic`

**Branch**: `feature/4003-podcast-episodes`

**Labels**: user-story, podcasts, backend


## Epic #5000: Monetization & Ad Insertion

Implement subscription plans with Stripe integration, paywall enforcement, and dynamic ad insertion for live and on-demand streams

### User Stories

#### Story #5001: As a DJ, I want to create subscription plans so that I can monetize my content

**Points**: 8 | **Type**: feature | **Status**: unstarted

Implement subscription plan creation endpoint with Stripe Product and Price API integration, storing plan metadata in PostgreSQL.

**Acceptance Criteria**:
1. Given a DJ with monetization:write scope, When they create plan with price_cents and billing_interval, Then Stripe Product and Price are created and IDs are stored
2. Given a plan with features array, When created, Then features are stored in JSONB column for flexible querying
3. Given a plan with monthly billing, When listener subscribes, Then Stripe Subscription is created with correct price_id
4. Given a plan update, When DJ modifies price, Then new Stripe Price is created and old one is archived

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for subscription plan creation and Stripe integration`
- 🟢 GREEN: `green: plan CRUD endpoints with Stripe API calls`
- 🔵 REFACTOR: `refactor: extract Stripe client wrapper and plan validation logic`

**Branch**: `feature/5001-subscription-plans`

**Labels**: user-story, monetization, stripe

#### Story #5002: As a listener, I want to subscribe to a DJ's plan so that I can access premium content

**Points**: 8 | **Type**: feature | **Status**: unstarted

Implement subscription creation endpoint with Stripe Customer and Subscription API, enforcing paywall on protected resources.

**Acceptance Criteria**:
1. Given a listener with payment method, When they subscribe to plan, Then Stripe Subscription is created and subscription_id is stored with status 'active'
2. Given a listener with active subscription, When they access paywalled stream, Then HLS manifest is served without 403 error
3. Given a listener with expired subscription, When they access paywalled content, Then 403 Forbidden is returned with renewal link
4. Given a subscription payment failure, When Stripe webhook fires invoice.payment_failed, Then subscription status is updated to 'past_due' and email is sent

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for subscription creation and paywall enforcement`
- 🟢 GREEN: `green: subscription endpoints with Stripe integration and middleware`
- 🔵 REFACTOR: `refactor: extract paywall middleware and subscription status checker`

**Branch**: `feature/5002-subscription-paywall`

**Labels**: user-story, monetization, security

#### Story #5003: As a DJ, I want to insert ads into my streams so that I can generate additional revenue

**Points**: 13 | **Type**: feature | **Status**: unstarted

Implement ad marker configuration endpoint and Relay Worker logic to dynamically insert ad segments at specified positions.

**Acceptance Criteria**:
1. Given a DJ with ad markers configured, When Relay Worker reaches position_seconds, Then FFmpeg input is switched to ad source for duration_seconds
2. Given a live stream with pre-roll ad, When listener connects, Then 30-second ad plays before main stream starts
3. Given a playlist with mid-roll ads, When listener reaches 15:00 timestamp, Then 60-second ad is inserted seamlessly
4. Given an ad insertion failure, When ad source is unreachable, Then main stream continues without interruption and error is logged

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for ad marker configuration and insertion logic`
- 🟢 GREEN: `green: ad marker endpoints and Relay Worker ad insertion`
- 🔵 REFACTOR: `refactor: extract ad scheduling logic and FFmpeg input switcher`

**Branch**: `feature/5003-ad-insertion`

**Labels**: user-story, monetization, ffmpeg, critical


## Epic #6000: Analytics & Monitoring

Implement comprehensive analytics for streams, playlists, and podcasts with Prometheus metrics and Grafana dashboards

### User Stories

#### Story #6001: As a DJ, I want to view listener analytics so that I can understand my audience engagement

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement analytics endpoints with date-range filtering, daily aggregates, and CSV export for streams, playlists, and podcasts.

**Acceptance Criteria**:
1. Given a stream with historical data, When DJ queries /analytics/streams/{id}?from=2025-06-01&to=2025-06-07, Then daily_listeners array and peak_listeners are returned
2. Given a playlist with play data, When DJ requests analytics, Then play_count per day and total_plays are returned
3. Given a podcast with downloads, When DJ queries analytics, Then daily_downloads and total_downloads are returned
4. Given analytics data, When DJ requests CSV export, Then downloadable CSV with date, metric, and value columns is generated

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for analytics aggregation and CSV export`
- 🟢 GREEN: `green: analytics endpoints with PostgreSQL aggregation queries`
- 🔵 REFACTOR: `refactor: extract analytics query builder and CSV generator`

**Branch**: `feature/6001-analytics-endpoints`

**Labels**: user-story, analytics, backend

#### Story #6002: As an Admin, I want Prometheus metrics so that I can monitor system health in Grafana

**Points**: 8 | **Type**: feature | **Status**: unstarted

Implement Prometheus metrics exporter with custom metrics for API servers and Relay Workers, exposing /metrics endpoint.

**Acceptance Criteria**:
1. Given API server running, When Prometheus scrapes /metrics, Then http_requests_total, http_request_duration_seconds, and db_connection_pool_usage are exposed
2. Given Relay Worker running, When metrics are scraped, Then relay_listener_count, hls_segment_generation_time_seconds, and cpu_usage_percent are exposed
3. Given high error rate (>1% 5xx), When Alertmanager evaluates rules, Then alert is triggered and sent to Slack/email
4. Given Relay Worker offline, When no heartbeat for 2 minutes, Then alert is triggered with stream_id and last_seen timestamp

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for Prometheus metrics and Alertmanager rules`
- 🟢 GREEN: `green: Prometheus exporter with custom metrics`
- 🔵 REFACTOR: `refactor: extract metrics collector and add Grafana dashboard JSON`

**Branch**: `feature/6002-prometheus-metrics`

**Labels**: user-story, monitoring, devops

#### Story #6003: As an Admin, I want structured logging so that I can debug issues efficiently

**Points**: 5 | **Type**: feature | **Status**: unstarted

Implement structured JSON logging with request IDs, user context, and error stack traces, forwarding logs to ELK or Datadog.

**Acceptance Criteria**:
1. Given an API request, When processed, Then log entry includes timestamp, request_id, user_id, path, status_code, and latency_ms
2. Given an error occurs, When logged, Then stack trace and error context are included in JSON format
3. Given Relay Worker event, When logged, Then relay_worker_id, stream_id, listener_count, and event type are included
4. Given logs forwarded to ELK, When queried in Kibana, Then logs are searchable by request_id, user_id, and error type

**TDD Workflow**:
- 🔴 RED: `WIP: red tests for structured logging and log forwarding`
- 🟢 GREEN: `green: JSON logging with context injection`
- 🔵 REFACTOR: `refactor: extract logging middleware and add log rotation`

**Branch**: `feature/6003-structured-logging`

**Labels**: user-story, logging, devops

