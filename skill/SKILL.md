---
name: adcp-advertising
description: Publish and manage advertising campaigns using the Ad Context Protocol (AdCP). Use when the user wants to create ads, buy media, discover advertising inventory, manage creative assets, or work with programmatic advertising. Supports MCP and A2A protocols for AI-powered advertising automation.
---

# Ad Context Protocol (AdCP) Advertising Skill

## Overview

This skill enables OpenClaw agents to publish and manage advertising campaigns using the Ad Context Protocol (AdCP). AdCP is an open standard for advertising automation that provides a unified interface for discovering inventory, creating campaigns, managing creatives, and optimizing performance across multiple advertising platforms.

**Official AdCP Repository**: https://github.com/adcontextprotocol/adcp  
**Official AdCP Documentation**: https://docs.adcontextprotocol.org  
**Complete Documentation Index**: https://docs.adcontextprotocol.org/llms.txt

## When to Use This Skill

Use this skill when the user wants to:
- Discover advertising inventory or media products
- Create, update, or manage advertising campaigns
- Upload and sync creative assets (images, videos, display ads)
- Monitor campaign performance and delivery metrics
- Work with programmatic advertising
- Buy media across different channels (display, video, CTV, audio, DOOH)
- Target specific audiences or demographics
- Manage advertising budgets and pricing

## Quick Start

### 1. Discover Agent Capabilities

Always start by discovering what the agent supports:

```javascript
const capabilities = await testAgent.getAdcpCapabilities({});
console.log(capabilities.media_buy.portfolio); // See supported publishers
console.log(capabilities.media_buy.channels);   // See available channels
```

### 2. Find Advertising Products

Use natural language to discover inventory:

```javascript
const result = await testAgent.getProducts({
  brief: 'Premium video inventory for tech brand targeting developers',
  brand_manifest: {
    name: 'Acme Corp',
    url: 'https://acme.com'
  },
  filters: {
    channels: ['display', 'ctv'],
    budget_range: { min: 5000, max: 50000 }
  }
});

// Review products
result.products.forEach(product => {
  console.log(`${product.name}: ${product.description}`);
  console.log(`Pricing: ${product.pricing_options}`);
});
```

### 3. Review Creative Requirements

Check what creative formats are needed:

```javascript
const formats = await testAgent.listCreativeFormats({
  format_types: ['video', 'display']
});

formats.forEach(format => {
  console.log(`${format.id}: ${format.width}x${format.height}`);
});
```

### 4. Create a Campaign

Create a media buy with your selected products:

```javascript
const campaign = await testAgent.createMediaBuy({
  buyer_ref: 'campaign-2026-q1-001',
  brand_manifest: {
    url: 'https://acme.com',
    name: 'Acme Corporation'
  },
  packages: [
    {
      buyer_ref: 'pkg-video-001',
      product_id: 'premium_video_30s',
      pricing_option_id: 'cpm-standard',
      budget: 10000
    }
  ],
  start_time: { type: 'asap' },
  end_time: '2026-03-31T23:59:59Z'
});

console.log(`Campaign created: ${campaign.media_buy_id}`);
console.log(`Status: ${campaign.status}`); // May be 'pending' for approval
```

### 5. Upload Creatives

Sync creative assets to the campaign:

```javascript
await testAgent.syncCreatives({
  creatives: [
    {
      creative_id: 'hero_video_30s',
      name: 'Brand Hero Video',
      format_id: {
        agent_url: 'https://creative.adcontextprotocol.org',
        id: 'video_standard_30s'
      },
      assets: {
        video: {
          url: 'https://cdn.example.com/hero.mp4',
          width: 1920,
          height: 1080,
          duration_ms: 30000
        }
      }
    }
  ],
  assignments: {
    'hero_video_30s': ['pkg-video-001']
  }
});
```

### 6. Monitor Performance

Track campaign delivery and metrics:

```javascript
const delivery = await testAgent.getMediaBuyDelivery({
  media_buy_id: campaign.media_buy_id,
  granularity: 'daily',
  date_range: {
    start: '2026-01-01',
    end: '2026-01-31'
  }
});

console.log(`Impressions: ${delivery.delivery.impressions}`);
console.log(`Spend: $${delivery.delivery.spend}`);
console.log(`CPM: $${delivery.delivery.cpm}`);
```

## Core Concepts

### The 8 Media Buy Tasks

AdCP provides 8 standardized tasks for the complete advertising lifecycle. Learn more in the [Media Buy Protocol documentation](https://docs.adcontextprotocol.org/docs/media-buy/).

1. **get_adcp_capabilities** - Discover agent features and portfolio (~1s)
2. **get_products** - Find inventory using natural language (~60s)
3. **list_creative_formats** - View creative specifications (~1s)
4. **create_media_buy** - Launch campaigns (minutes-days, may require approval)
5. **update_media_buy** - Modify campaigns (minutes-days)
6. **sync_creatives** - Upload creative assets (minutes-days)
7. **list_creatives** - Query creative library (~1s)
8. **get_media_buy_delivery** - Track performance (~60s)

**Complete task reference**: https://docs.adcontextprotocol.org/docs/media-buy/task-reference/

### Brand Manifest

Brand context can be provided two ways:

**URL reference** (recommended - agent fetches brand info):
```json
{
  "brand_manifest": {
    "url": "https://brand.com"
  }
}
```

**Inline manifest** (full brand details):
```json
{
  "brand_manifest": {
    "name": "Brand Name",
    "url": "https://brand.com",
    "tagline": "Brand tagline",
    "colors": { "primary": "#FF0000" },
    "logo": { "url": "https://cdn.brand.com/logo.png" }
  }
}
```

### Pricing Models

Products support various pricing models:
- **CPM** (Cost Per Mille/Thousand) - Fixed price per 1000 impressions
- **CPM-Auction** - Bid-based pricing for impressions
- **CPCV** (Cost Per Completed View) - Video completions
- **Flat-Fee** - Fixed campaign cost
- **CPP** (Cost Per Point) - Percentage of audience reached

For auction pricing, include `bid_price` in your package.

### Asynchronous Operations

AdCP is **not a real-time protocol**. Operations may take:
- **~1 second** - Simple lookups (formats, creative lists)
- **~60 seconds** - AI/inference operations (product discovery)
- **Minutes to days** - Operations requiring human approval (campaign creation)

Always check the `status` field in responses:
- `completed` - Operation finished successfully
- `pending` - Awaiting approval or processing
- `failed` - Operation failed (check error details)

### Targeting Capabilities

Apply targeting overlays to campaigns:
```javascript
{
  targeting_overlay: {
    geo: {
      included: ['US-CA', 'US-NY'],  // DMA codes or regions
      excluded: ['US-TX']
    },
    demographics: {
      age_ranges: [{ min: 25, max: 44 }],
      genders: ['M', 'F']
    },
    behavioral: {
      interests: ['technology', 'gaming'],
      purchase_intent: ['consumer_electronics']
    },
    contextual: {
      keywords: ['innovation', 'design'],
      categories: ['IAB19'] // Technology & Computing
    }
  }
}
```

## Common Workflows

### Workflow 1: Campaign Discovery to Launch

```javascript
// 1. Discover capabilities
const caps = await agent.getAdcpCapabilities({});

// 2. Find products
const products = await agent.getProducts({
  brief: 'Q1 2026 brand awareness campaign for tech startup',
  brand_manifest: { url: 'https://startup.com' },
  filters: { channels: ['display', 'video'] }
});

// 3. Check creative formats
const formats = await agent.listCreativeFormats({
  format_types: ['display', 'video']
});

// 4. Create campaign
const campaign = await agent.createMediaBuy({
  buyer_ref: 'q1-2026-awareness',
  brand_manifest: { url: 'https://startup.com' },
  packages: [
    {
      buyer_ref: 'pkg-001',
      product_id: products.products[0].product_id,
      pricing_option_id: 'cpm-standard',
      budget: 15000
    }
  ],
  start_time: { type: 'asap' },
  end_time: '2026-03-31T23:59:59Z'
});

// 5. Upload creatives
await agent.syncCreatives({
  creatives: [...], // Your creative assets
  assignments: {
    'creative_001': ['pkg-001']
  }
});

// 6. Monitor performance
const delivery = await agent.getMediaBuyDelivery({
  media_buy_id: campaign.media_buy_id
});
```

### Workflow 2: Update Running Campaign

```javascript
// Pause, adjust budget, and resume campaign
await agent.updateMediaBuy({
  media_buy_id: 'mb_abc123',
  updates: {
    status: 'paused',
    budget_change: 5000, // Add $5000
    end_time: '2026-04-30T23:59:59Z'
  }
});

// Resume after adjustments
await agent.updateMediaBuy({
  media_buy_id: 'mb_abc123',
  updates: { status: 'active' }
});
```

**More workflow examples**: See [EXAMPLES.md](EXAMPLES.md) for complete campaign scenarios including creative management, multi-channel campaigns, and optimization workflows.

## Test Agent

For development and testing, use the public test agent:

**Agent URL**: `https://test-agent.adcontextprotocol.org/mcp`  
**Auth Token**: `1v8tAhASaUYYp4odoQ1PnMpdqNaMiTrCRqYo9OJp6IQ`

```javascript
import { testAgent } from '@adcp/client/testing';

// No authentication needed for test agent
const result = await testAgent.getProducts({
  brief: 'Test campaign',
  brand_manifest: { url: 'https://example.com' }
});
```

Interactive testing available at: **[testing.adcontextprotocol.org](https://testing.adcontextprotocol.org)**

## Error Handling

Common error patterns:

**400 Bad Request** - Invalid parameters:
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "budget must be greater than 0",
    "field": "packages[0].budget"
  }
}
```

**401 Unauthorized** - Missing or invalid auth:
```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid authentication token"
  }
}
```

**404 Not Found** - Invalid ID reference:
```json
{
  "error": {
    "code": "NOT_FOUND",
    "message": "Product not found",
    "resource": "product_id: premium_video_30s"
  }
}
```

Always check for errors before processing responses:
```javascript
if (result.error) {
  console.error(`Error: ${result.error.message}`);
  return;
}
```

## Best Practices

### 1. Always Start with Capabilities

Call `get_adcp_capabilities` first to understand what the agent supports before making other requests.

### 2. Use Clear Buyer References

Use descriptive `buyer_ref` values for tracking:
- Good: `'campaign-2026-q1-tech-launch'`
- Avoid: `'c1'`, `'test'`, `'abc'`

### 3. Handle Async Operations

Check `status` field and implement polling for pending operations:
```javascript
let status = 'pending';
while (status === 'pending') {
  await sleep(5000); // Wait 5 seconds
  const update = await agent.getMediaBuyDelivery({
    media_buy_id: campaign.media_buy_id
  });
  status = update.status;
}
```

### 4. Write Detailed Briefs

Better briefs lead to better product matches:
- Good: `'Premium video inventory for luxury automotive brand targeting high-income professionals aged 35-54 in major metros. Focus on brand awareness with completion rates above 70%.'`
- Avoid: `'video ads'`, `'need advertising'`

### 5. Validate Creative Formats

Always check `list_creative_formats` to ensure your creatives meet requirements before uploading.

### 6. Monitor Budget Pacing

Regularly check delivery metrics to ensure campaigns are pacing properly:
```javascript
const delivery = await agent.getMediaBuyDelivery({
  media_buy_id: campaign.media_buy_id
});

const pacing = delivery.delivery.spend / delivery.delivery.budget;
console.log(`Budget pacing: ${(pacing * 100).toFixed(1)}%`);
```

## Additional Resources

### Official AdCP Documentation
- **Main Documentation**: https://docs.adcontextprotocol.org
- **Complete Index**: https://docs.adcontextprotocol.org/llms.txt
- **Media Buy Protocol**: https://docs.adcontextprotocol.org/docs/media-buy/
- **Quick Reference**: https://docs.adcontextprotocol.org/docs/media-buy/quick-reference
- **Task Reference**: https://docs.adcontextprotocol.org/docs/media-buy/task-reference/
- **Quickstart Guide**: https://docs.adcontextprotocol.org/docs/quickstart

### This Skill's Documentation
- [REFERENCE.md](REFERENCE.md) - Complete API reference and schemas
- [EXAMPLES.md](EXAMPLES.md) - Real-world campaign examples
- [PROTOCOLS.md](PROTOCOLS.md) - MCP vs A2A protocol details
- [TARGETING.md](TARGETING.md) - Advanced targeting strategies
- [CREATIVE.md](CREATIVE.md) - Creative asset management guide

## Key Reminders

1. **AdCP is asynchronous** - Operations may take minutes to days
2. **Human approval may be required** - Check for `pending` status
3. **Start with capabilities** - Always call `get_adcp_capabilities` first
4. **Brand context matters** - Provide detailed brand manifests for better results
5. **Targeting is additive** - Product targeting + your overlay = final targeting
6. **Creative formats are strict** - Always validate against format specifications
7. **Monitor performance** - Regular delivery checks ensure campaign success

## Support

For help with AdCP:
- Official Repository: https://github.com/adcontextprotocol/adcp
- Documentation: https://docs.adcontextprotocol.org
- Interactive Testing: https://testing.adcontextprotocol.org
- Complete API Docs: https://docs.adcontextprotocol.org/llms.txt
