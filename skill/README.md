# Ad Context Protocol (AdCP) Advertising Skill for OpenClaw

A comprehensive skill that enables OpenClaw agents to publish and manage advertising campaigns using the Ad Context Protocol (AdCP).

**Official AdCP Repository**: [github.com/adcontextprotocol/adcp](https://github.com/adcontextprotocol/adcp)  
**Official AdCP Documentation**: https://docs.adcontextprotocol.org  
**Complete Documentation Index**: https://docs.adcontextprotocol.org/llms.txt

## Overview

This skill provides OpenClaw agents with the ability to:
- 🔍 **Discover advertising inventory** using natural language
- 🎯 **Create and manage campaigns** across multiple channels (display, video, CTV, audio, DOOH)
- 🎨 **Upload and manage creative assets** (images, videos, HTML5)
- 📊 **Monitor campaign performance** with real-time metrics
- 🎛️ **Optimize campaigns** based on performance data
- 🌐 **Target audiences** with advanced demographic, behavioral, and contextual targeting

## Quick Start

### Installation

For ClawHub users:
```bash
# Install via ClawHub
openclaw skills install adcp-advertising
```

For local development:
```bash
# Clone or download this skill to your workspace
cd ~/.openclaw/workspace/skills/
git clone <this-repo> adcp-advertising
```

### Your First Ad Campaign (5 Minutes)

Get your first test ad running in minutes using the public test agent:

**Step 1: Start without any credentials**
```
"Show me what advertising capabilities are available"
```
This discovers what the test agent can do - no authentication needed.

**Step 2: Discover inventory**
```
"Find display advertising inventory for a tech startup, budget around $5000"
```
The agent will search products and show you options with pricing.

**Step 3: Create your campaign**
```
"Create a campaign with Product ID prod_abc123, $5000 budget, 
targeting tech professionals in California"
```
The test agent will create a campaign (this uses the test token automatically).

**Step 4: Upload a creative**
```
"Upload this banner image as a creative for the campaign"
```
Attach your banner image, and it will be uploaded and assigned.

**Step 5: Check performance**
```
"Show me campaign performance and metrics"
```
Monitor impressions, clicks, spend, and pacing.

**That's it!** You've launched your first campaign using the test environment.

### Moving to Production

When ready for real campaigns:
1. Find sales agents via `get_adcp_capabilities` on production endpoints
2. Contact their sales team for credentials
3. Replace test agent URL with production agent URL
4. Use your production Bearer token for authentication

## Features

### 🔍 Product Discovery
- Natural language search for advertising inventory
- Filter by channel, budget, format, and date range
- Detailed product information including pricing and targeting options

### 🎯 Campaign Management
- Create campaigns across multiple channels
- Update budgets, targeting, and creative assignments
- Pause/resume campaigns
- Schedule campaigns for future launch

### 🎨 Creative Management
- Support for all standard IAB formats (display, video, native)
- Bulk creative upload
- Creative library management
- Performance tracking by creative

### 📊 Performance Monitoring
- Real-time campaign metrics
- Detailed breakdowns by package, creative, and geography
- Budget pacing alerts
- Daily/hourly granularity

### 🎛️ Optimization
- Automatic budget reallocation based on performance
- A/B testing for creatives
- Targeting optimization recommendations
- Pacing adjustments

## Supported Channels

- **Display**: Banner ads, rich media, HTML5
- **Video**: Pre-roll, mid-roll, outstream
- **CTV**: Connected TV advertising
- **Audio**: Streaming audio, podcast ads
- **Native**: In-feed, content-style ads
- **DOOH**: Digital out-of-home advertising

## Documentation

Comprehensive documentation is included with this skill:

- **[SKILL.md](SKILL.md)** - Main skill guide with quick start and core concepts
- **[REFERENCE.md](REFERENCE.md)** - Complete API reference for all 8 AdCP tasks
- **[EXAMPLES.md](EXAMPLES.md)** - Real-world campaign examples and use cases
- **[PROTOCOLS.md](PROTOCOLS.md)** - MCP vs A2A protocol details
- **[TARGETING.md](TARGETING.md)** - Advanced targeting strategies
- **[CREATIVE.md](CREATIVE.md)** - Creative asset management guide

## Example Workflows

### Launch a Simple Campaign

```
Agent: "I need to run a display campaign for my startup"

System discovers products, shows options

Agent: "Create a campaign with Product 1, $10,000 budget, 
       targeting tech professionals in California"

System creates campaign

Agent: "Upload my 300x250 banner to this campaign"

System uploads creative and assigns to campaign
```

### Monitor and Optimize

```
Agent: "Show me how my video campaign is performing"

System shows metrics: impressions, CTR, spend, pacing

Agent: "Which creative is performing best?"

System analyzes and shows creative performance rankings

Agent: "Shift $5,000 from package B to package A 
       since it's performing better"

System updates budget allocation
```

## Authentication

AdCP uses a tiered authentication model - some operations are public, others require credentials.

### Public Operations (No Auth Required)

These work without any credentials:

- **`get_adcp_capabilities`** - Discover agent capabilities and portfolio
- **`list_creative_formats`** - Browse available ad formats  
- **`get_products`** (limited) - Basic inventory discovery (partial catalog, no pricing)

**Why?** Publishers want potential buyers to explore capabilities before establishing relationships.

### Authenticated Operations (Credentials Required)

Everything else needs authentication:

- **`get_products`** (full) - Complete catalog with pricing and custom products
- **`create_media_buy`** - Create advertising campaigns
- **`update_media_buy`** - Modify existing campaigns
- **`sync_creatives`** - Upload creative assets
- **`list_creatives`** - View your creative library
- **`get_media_buy_delivery`** - Monitor campaign performance
- **`provide_performance_feedback`** - Submit optimization signals

### Authentication Method

AdCP uses **Bearer token authentication**:

```
Authorization: Bearer <your-token>
```

Tokens can be:
- **Opaque tokens**: Server-validated strings
- **JWT tokens**: Self-contained with embedded claims

### Test Agent (Public Credentials)

A public test agent is available with shared credentials for development:

- **Agent URL**: `https://test-agent.adcontextprotocol.org/mcp`
- **Auth Token**: `1v8tAhASaUYYp4odoQ1PnMpdqNaMiTrCRqYo9OJp6IQ`
- **Interactive Testing**: [testing.adcontextprotocol.org](https://testing.adcontextprotocol.org)

This token is **intentionally public** - anyone can use it for testing. It's included in package.json and official AdCP documentation.

### Production Credentials

For real campaigns, you need credentials from each sales agent:

1. **Discover agents**: Use `get_adcp_capabilities` on production endpoints
2. **Contact sales**: Reach out to the agent's sales/partnerships team
3. **Complete onboarding**: Provide business info, sign agreements, configure billing
4. **Receive credentials**: Get your API Bearer token or OAuth credentials
5. **Store securely**: Use environment variables or secret managers (never commit to git)

**Important**: Each sales agent manages credentials independently. You need separate auth for each one you work with.

### Example Configuration

**Test environment:**
```json
{
  "agent_url": "https://test-agent.adcontextprotocol.org/mcp",
  "auth": {
    "type": "bearer",
    "token": "1v8tAhASaUYYp4odoQ1PnMpdqNaMiTrCRqYo9OJp6IQ"
  }
}
```

**Production environment:**
```json
{
  "agent_url": "https://sales-agent.example.com/mcp",
  "auth": {
    "type": "bearer",
    "token": "your-production-token-here"
  }
}
```

For more details, see the [official authentication guide](https://docs.adcontextprotocol.org/docs/building/integration/authentication).

## Key Concepts

### Asynchronous Operations

AdCP is **not a real-time protocol**. Operations may take:
- **~1 second**: Simple lookups (formats, creative lists)
- **~60 seconds**: AI operations (product discovery)
- **Minutes to days**: Operations requiring approval (campaign creation)

Always check the `status` field and handle `pending` states.

### Targeting is Additive

Your targeting overlay + Product targeting = Final targeting

Products already have targeting. Your overlay adds constraints.

### Brand Context Matters

Provide detailed brand manifests for better product matches:

```javascript
{
  brand_manifest: {
    name: 'Acme Corp',
    url: 'https://acme.com',
    tagline: 'Innovation that matters',
    colors: { primary: '#FF4500' }
  }
}
```

## Who Should Use This Skill?

### Marketing Teams
- Launch campaigns faster without learning complex platforms
- Monitor multiple campaigns through conversational queries
- Get instant performance insights and optimization recommendations

### Media Buyers
- Discover inventory across multiple publishers at once
- Compare products and pricing using natural language
- Automate routine optimization tasks

### Agencies
- Manage client campaigns through AI agents
- Scale operations without proportional staff increases
- Standardize workflows across different platforms

### Developers
- Build advertising automation tools
- Integrate ad buying into larger workflows
- Access advertising APIs through natural language

## Common Use Cases

### Launch New Product Campaign
```
Agent: "I need to launch a campaign for our new SaaS product targeting 
        CTOs and tech directors in major US cities, $50,000 budget"

System: Discovers suitable products, creates multi-package campaign, 
        sets up targeting, and uploads provided creatives
```

### Optimize Existing Campaign
```
Agent: "Analyze my video campaign performance and recommend optimizations"

System: Reviews metrics, identifies top performers, suggests budget 
        reallocation, pauses underperforming elements
```

### Multi-Channel Strategy
```
Agent: "Create an omnichannel campaign: display in California, 
        video in major cities, audio during commute hours"

System: Creates coordinated campaign across channels with unified 
        targeting and creative strategy
```

## Requirements

- **OpenClaw**: Compatible with OpenClaw 2026.1.0+
- **Node.js**: 18+ (for JavaScript examples)
- **Python**: 3.9+ (for Python examples)

## Support & Resources

### Official AdCP Resources
- **Official Repository**: https://github.com/adcontextprotocol/adcp
- **Main Documentation**: https://docs.adcontextprotocol.org
- **Complete Index (for AI agents)**: https://docs.adcontextprotocol.org/llms.txt
- **Media Buy Protocol**: https://docs.adcontextprotocol.org/docs/media-buy/
- **Task Reference**: https://docs.adcontextprotocol.org/docs/media-buy/task-reference/
- **Quickstart Guide**: https://docs.adcontextprotocol.org/docs/quickstart
- **Interactive Testing**: https://testing.adcontextprotocol.org

### This Skill Repository
- **Repository**: https://github.com/edyyy62/openclaw-adcp
- **Issues**: https://github.com/edyyy62/openclaw-adcp/issues

### OpenClaw Resources
- **OpenClaw Docs**: https://docs.openclaw.ai
- **ClawHub**: https://www.clawhub.ai/

## License

This skill is provided under the MIT License. See [LICENSE](LICENSE) for details.

## Contributing

Contributions are welcome! Please submit issues or pull requests on GitHub.

## Version

**Version**: 1.0.0  
**Last Updated**: January 2026  
**AdCP Version**: 3.x compatible

## Author

Created for the OpenClaw community to enable AI-powered advertising automation.

## Acknowledgments

- Ad Context Protocol team for the comprehensive advertising API
- OpenClaw community for the excellent AI assistant framework
- ClawHub for skill distribution infrastructure
