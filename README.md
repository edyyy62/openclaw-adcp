# OpenClaw AdCP Advertising Skill

This repository contains an OpenClaw skill for AI-powered advertising automation using the Ad Context Protocol (AdCP).

**Official AdCP Repository**: https://github.com/adcontextprotocol/adcp

## Repository Structure

```
/
├── skill/              # Complete ClawHub skill package (ready to submit)
│   ├── SKILL.md        # Main skill documentation
│   ├── package.json    # Skill metadata
│   ├── LICENSE         # MIT License
│   ├── README.md       # Skill overview
│   └── ...             # Additional documentation
│
└── SUBMISSION_GUIDE.md # ClawHub submission checklist (not for submission)
```

## For ClawHub Submission

The `skill/` directory contains everything needed for ClawHub:

```bash
# Create submission package
cd skill
zip -r ../adcp-advertising-v1.0.0.zip . -x "*.DS_Store"
```

Or upload the `skill/` directory directly to ClawHub.

## Quick Links

- **Documentation**: https://docs.adcontextprotocol.org
- **This Skill**: See `skill/README.md` for full documentation
- **Issues**: https://github.com/edyyy62/openclaw-adcp/issues

## License

MIT License - See `skill/LICENSE`
