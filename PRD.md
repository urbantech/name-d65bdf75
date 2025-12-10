# Product Requirements Document (PRD)

## Web-Based Audio Streaming API for DJs & Podcasters

---

```json
{
  "document_metadata": {
    "title": "Web-Based Audio Streaming API for DJs & Podcasters",
    "version": "1.0",
    "date": "2025-06-03",
    "author": "Product Architecture Team",
    "status": "Final",
    "document_type": "Product Requirements Document"
  },

  "executive_summary": {
    "overview": "A comprehensive RESTful API service enabling DJs and podcasters to live-stream audio via Shoutcast relay, manage on-demand playlists with direct file uploads, host podcasts with custom domains, and monetize content through subscriptions and ad insertion. The platform provides real-time analytics, automated HLS transcoding, and enterprise-grade security.",
    
    "business_objectives": [
      "Provide a scalable, cloud-based audio streaming infrastructure for DJs and podcasters",
      "Enable monetization through subscription plans and dynamic ad insertion",
      "Offer custom domain support with automated DNS and TLS provisioning for professional branding",
      "Deliver comprehensive analytics for listener engagement and content performance",
      "Ensure 99.9% uptime with horizontal scalability for tens of thousands of concurrent listeners"
    ],
    
    "key_differentiators": [
      "Integrated Shoutcast relay with real-time HLS segmentation via FFmpeg",
      "Direct audio file uploads with automated transcoding to HLS and MP3",
      "Custom domain support for podcast RSS feeds with automated TLS certificates",
      "Built-in monetization with Stripe integration and dynamic ad insertion",
      "Multi-format streaming support (HLS, continuous MP3, RSS 2.0)",
      "GDPR-compliant data handling with Right to Be Forgotten"
    ],
    
    "success_criteria": [
      "Support 10,000+ concurrent listeners per stream with <2s latency",
      "Achieve 99.9% API uptime",
      "Process file uploads and HLS transcoding within 5 minutes for 100MB files",
      "Enable custom domain provisioning within 15 minutes",
      "Maintain <200ms API response time for 95% of requests"
    ]
  },

  "product_overview": {
    "vision": "To become the leading API-first platform for audio streaming, empowering DJs and podcasters with professional-grade tools for live broadcasting, on-demand content delivery, and audience monetization.",
    
    "core_capabilities": {
      "live_streaming": {
        "description": "Real-time audio relay from Shoutcast sources with HLS segmentation",
        "features": [
          "Shoutcast source registration and validation",
          "FFmpeg-based HLS segmentation (6-second chunks)",
          "Continuous MP3 proxy streaming",
          "Real-time metadata extraction (current track, bitrate)",
          "Listener count tracking and analytics",
          "Public and private stream visibility",
          "Paywall enforcement for premium streams"
        ]
      },
      
      "on_demand_playlists": {
        "description": "Curated audio playlists with direct file uploads and external URL support",
        "features": [
          "Direct MP3 file upload (up to 100MB per file)",
          "Automated HLS transcoding with background job processing",
          "External URL reference support for tracks",
          "HLS manifest generation and caching",
          "Continuous MP3 stream concatenation",
          "Track metadata management (title, artist, duration)",
          "Playlist visibility controls (public/private)",
          "Monetization and paywall support"
        ]
      },
      
      "podcast_hosting": {
        "description": "Full-featured podcast hosting with custom domain support and RSS 2.0 generation",
        "features": [
          "Podcast series creation and management",
          "Episode upload via direct file or external URL",
          "Automated RSS 2.0 XML generation",
          "Custom domain registration (e.g., podcast.djname.com)",
          "Automated DNS CNAME/TXT record configuration",
          "Let's Encrypt TLS certificate provisioning via ACME",
          "iTunes-compatible RSS tags (itunes:* namespace)",
          "Episode-level monetization controls",
          "RSS feed caching on CDN (300s TTL)"
        ]
      },
      
      "monetization": {
        "description": "Integrated subscription management and dynamic ad insertion",
        "features": [
          "Stripe-powered subscription plans (monthly/yearly)",
          "JWT-based paywall enforcement",
          "Dynamic ad insertion with configurable markers (pre-roll, mid-roll, post-roll)",
          "Ad slot duration and position configuration",
          "Subscription status webhooks from Stripe",
          "Revenue analytics and reporting",
          "Signed URLs for paywalled content"
        ]
      },
      
      "analytics": {
        "description": "Comprehensive metrics for streams, playlists, and podcasts",
        "features": [
          "Real-time listener count tracking",
          "Daily aggregated play counts and downloads",
          "Peak listener metrics",
          "Date-range query support",
          "CSV and JSON export formats",
          "Two-year data retention with 90-day raw log purge",
          "Grafana dashboard integration"
        ]
      }
    },
    
    "technical_architecture": {
      "api_layer": {
        "framework": "FastAPI (Python 3.11+)",
        "authentication": "JWT (RS256) with refresh tokens",
        "authorization": "Role-based access control (RBAC) with scopes",
        "validation": "Pydantic models for request/response schemas",
        "rate_limiting": "Redis-based (100 req/min metadata, 10 req/min writes)"
      },
      
      "relay_workers": {
        "container": "Docker with FFmpeg pre-installed",
        "orchestration": "Kubernetes Deployments with HPA",
        "scaling": "Horizontal autoscaling based on CPU (>80%) and listener count (>5000/pod)",
        "heartbeat": "30-second interval with 2-minute timeout",
        "hls_segmentation": "FFmpeg with 6-second TS chunks, 3-segment rolling window"
      },
      
      "data_layer": {
        "primary_database": "PostgreSQL 14+ (AWS RDS Multi-AZ)",
        "connection_pooling": "PgBouncer (max 100 connections)",
        "caching": "Redis 7+ for metadata and rate limiting",
        "object_storage": "AWS S3 with lifecycle policies",
        "cdn": "AWS CloudFront with custom cache rules"
      },
      
      "infrastructure": {
        "cloud_provider": "AWS (primary), multi-region support",
        "container_orchestration": "Amazon EKS (Kubernetes 1.27+)",
        "load_balancer": "AWS Application Load Balancer with SSL termination",
        "dns": "AWS Route 53 for custom domain management",
        "tls_certificates": "Let's Encrypt via ACME (Certbot)",
        "monitoring": "Prometheus + Grafana + ELK Stack",
        "secrets_management": "AWS KMS for encryption at rest"
      }
    }
  },

  "target_users": {
    "primary_personas": [
      {
        "persona_name": "Professional DJ",
        "demographics": {
          "age_range": "25-45",
          "technical_proficiency": "Intermediate to Advanced",
          "location": "Global, primarily urban centers"
        },
        "goals": [
          "Stream live DJ sets to global audience",
          "Monetize premium content through subscriptions",
          "Track listener engagement and growth",
          "Maintain professional brand with custom domain"
        ],
        "pain_points": [
          "Complex setup for live streaming infrastructure",
          "Lack of integrated monetization tools",
          "Difficulty tracking listener analytics",
          "High costs for dedicated streaming servers"
        ],
        "use_cases": [
          "Register weekly live stream with Shoutcast source",
          "Upload recorded sets as on-demand playlists",
          "Configure subscription paywall for exclusive content",
          "View real-time listener counts during live sets"
        ]
      },
      
      {
        "persona_name": "Podcast Producer",
        "demographics": {
          "age_range": "28-50",
          "technical_proficiency": "Beginner to Intermediate",
          "location": "Global, content creators"
        },
        "goals": [
          "Host podcast with custom branded domain",
          "Distribute episodes via RSS to Apple Podcasts, Spotify",
          "Monetize through ads and premium episodes",
          "Analyze download trends and audience demographics"
        ],
        "pain_points": [
          "Manual RSS feed generation and updates",
          "Complex DNS and SSL certificate setup",
          "Limited analytics from podcast platforms",
          "Difficulty inserting dynamic ads"
        ],
        "use_cases": [
          "Create podcast series with custom domain (podcast.example.com)",
          "Upload episodes with automated RSS regeneration",
          "Configure mid-roll ad markers for monetization",
          "Export monthly download reports for sponsors"
        ]
      },
      
      {
        "persona_name": "API Developer / Integrator",
        "demographics": {
          "age_range": "24-40",
          "technical_proficiency": "Advanced",
          "location": "Global, software development teams"
        },
        "goals": [
          "Integrate streaming API into custom DJ dashboard",
          "Build mobile apps for podcast listening",
          "Automate content uploads and management",
          "Implement custom analytics dashboards"
        ],
        "pain_points": [
          "Incomplete API documentation",
          "Lack of SDKs for common languages",
          "Complex authentication flows",
          "Inconsistent error responses"
        ],
        "use_cases": [
          "Consume OpenAPI spec for code generation",
          "Implement OAuth 2.0 flow for user authentication",
          "Build React dashboard using official Node.js SDK",
          "Set up webhook listeners for Stripe events"
        ]
      }
    ],
    
    "secondary_personas": [
      {
        "persona_name": "Platform Administrator",
        "role": "Manage users, monitor system health, handle billing",
        "key_responsibilities": [
          "User account management and support",
          "System monitoring and incident response",
          "Billing and subscription management",
          "Content moderation and compliance"
        ]
      },
      
      {
        "persona_name": "End Listener",
        "role": "Consume live streams, playlists, and podcasts",
        "key_interactions": [
          "Access HLS streams via web/mobile players",
          "Subscribe to premium content",
          "Download podcast episodes via RSS",
          "Experience inserted ads during playback"
        ]
      }
    ]
  },

  "functional_requirements": {
    "authentication_authorization": {
      "priority": "Critical",
      "user_stories": [
        {
          "id": "AUTH-001",
          "story": "As a DJ, I want to register an account so that I can manage my streams and content",
          "acceptance_criteria": [
            "POST /v1/auth/register accepts email, password, name",
            "Password must be ≥8 characters with uppercase, lowercase, number, special char",
            "Email validation via regex and uniqueness check",
            "Returns 201 with user_id and role='dj'",
            "Sends verification email (optional phase 2)"
          ],
          "api_endpoint": "POST /v1/auth/register"
        },
        
        {
          "id": "AUTH-002",
          "story": "As a DJ, I want to log in with my credentials so that I can access the API",
          "acceptance_criteria": [
            "POST /v1/auth/login accepts email and password",
            "Returns JWT access token (24h expiry) and refresh token",
            "JWT includes user_id, role, and scopes",
            "Returns 401 for invalid credentials",
            "Rate limit: 5 failed attempts per 15 minutes per IP"
          ],
          "api_endpoint": "POST /v1/auth/login"
        },
        
        {
          "id": "AUTH-003",
          "story": "As a DJ, I want to refresh my access token so that I can maintain authenticated sessions",
          "acceptance_criteria": [
            "POST /v1/auth/refresh accepts refresh_token",
            "Returns new access token with same scopes",
            "Invalidates old refresh token and issues new one",
            "Returns 401 if refresh token expired or invalid"
          ],
          "api_endpoint": "POST /v1/auth/refresh"
        },
        
        {
          "id": "AUTH-004",
          "story": "As a DJ, I want role-based access control so that I can only manage my own resources",
          "acceptance_criteria": [
            "Middleware validates JWT on all protected endpoints",
            "Scopes enforced: streams:read, streams:write, playlists:read, playlists:write, podcasts:read, podcasts:write, monetization:read, monetization:write, analytics:read",
            "DJs can only access their own resources (user_id match)",
            "Admins have global access to all resources",
            "Returns 403 Forbidden for insufficient permissions"
          ],
          "implementation": "FastAPI dependency injection with JWT validation"
        }
      ]
    },
    
    "live_streaming": {
      "priority": "Critical",
      "user_stories": [
        {
          "id": "STREAM-001",
          "story": "As a DJ, I want to register a Shoutcast source so that I can relay my live stream",
          "acceptance_criteria": [
            "POST /v1/streams accepts name, shoutcast_url, mount, stream_key, genre, description, visibility, monetization_enabled",
            "Validates Shoutcast URL reachability via HEAD request",
            "Encrypts stream_key using AWS KMS before storage",
            "Creates Kubernetes Deployment for Relay Worker Pod",
            "Returns 201 with stream_id and status='registered'",
            "Returns 422 if Shoutcast source unreachable"
          ],
          "api_endpoint": "POST /v1/streams",
          "technical_notes": "Relay Worker connects to Shoutcast, starts FFmpeg HLS segmentation"
        },
        
        {
          "id": "STREAM-002",
          "story": "As a DJ, I want to retrieve live stream metadata so that I can display current track and listener count",
          "acceptance_criteria": [
            "GET /v1/streams/{stream_id}/status returns current_track, bitrate, listeners, uptime, relay_connected",
            "Queries Shoutcast admin endpoint for metadata",
            "Queries Relay Worker for listener count",
            "Returns 503 if Relay Worker offline or Shoutcast unreachable",
            "Response time <500ms"
          ],
          "api_endpoint": "GET /v1/streams/{stream_id}/status"
        },
        
        {
          "id": "STREAM-003",
          "story": "As a listener, I want to access a live HLS stream so that I can listen in my browser or mobile app",
          "acceptance_criteria": [
            "GET /v1/streams/{stream_id}/live.m3u8 returns HLS manifest",
            "Manifest lists 3 most recent TS segments (6s each)",
            "CDN caches manifest with TTL=30s",
            "Enforces JWT authentication if visibility='private'",
            "Enforces subscription check if monetization_enabled=true",
            "Returns 403 Forbidden if unauthorized"
          ],
          "api_endpoint": "GET /v1/streams/{stream_id}/live.m3u8"
        },
        
        {
          "id": "STREAM-004",
          "story": "As a listener, I want to access a continuous MP3 stream so that I can listen with legacy players",
          "acceptance_criteria": [
            "GET /v1/streams/{stream_id}/live