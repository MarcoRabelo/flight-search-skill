# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.0.0] - 2026-03-01

### ✨ Added
- Initial release of Flight Search skill
- Amadeus API integration for flight search
- AviationStack API integration for flight status
- Price monitoring and alerts
- 3 bash scripts (search, monitor, status)
- Complete documentation suite
- MIT License

### 🔒 Security
- **[P1] Fixed command injection vulnerability** in `search_flights.sh`
  - Removed `eval` usage
  - Direct argument passing
  - Safe from shell metacharacter injection
- **[P1] Fixed command injection vulnerability** in `check_status.sh`
  - Removed `eval` usage
  - Direct argument passing
  - Safe from shell metacharacter injection
- **[P1] Fixed Python injection vulnerability** in `monitor_price.sh`
  - Removed shell variable interpolation in heredoc
  - Pass data via `sys.argv` instead
  - Safe from Python code injection
- **[P1] Fixed cleartext API key transmission** in AviationStack client
  - Changed default from HTTP to HTTPS
  - Prevents credential interception on public networks
  - Protects against man-in-the-middle attacks
- **[P1] Fixed config.json tracked in git** (credential leak risk)
  - Added `config.json` to `.gitignore`
  - Prevents accidental API key commits
  - Created `config.example.json` as template
  - Added `QUICKSTART.md` with security instructions
- **[P1] Fixed AviationStack config missing guard**
  - Check if aviationstack config exists before accessing
  - Friendly error message instead of KeyError crash
  - Minimal configs (Amadeus-only) now work correctly
- **[P2] Fixed hardcoded AviationStack URL**
  - Now reads `base_url` from config.json
  - Supports HTTPS/proxies/custom hosts
  - Configuration is now respected
- **[P2] Fixed numeric flight number handling**
  - Auto-detect IATA code (AA123) vs flight number (123)
  - Numeric inputs now use correct API parameter
  - Matches documented behavior
- **[P2] Fixed hardcoded alert threshold**
  - Now reads `price_drop_threshold_percent` from config.json
  - Honors user configuration
  - No more hardcoded 5% value
- **[P2] Fixed monitoring baseline calculation**
  - Find minimum price instead of assuming data[0]
  - Correct baseline even if API returns unsorted
  - Accurate price drop alerts
- **[P2] Fixed HTTP URLs in documentation**
  - Updated CONFIGURATION.md to use HTTPS
  - Prevents users from copy-pasting insecure URLs
  - Consistent with secure defaults
- **[P2] Fixed silent failures in monitor bootstrap**
  - Removed stderr suppression (2>/dev/null)
  - Show actionable error messages on search failures
  - Help users debug auth/network/config issues
- **[P1] Fixed JSON contamination from stderr**
  - Removed 2>&1 redirection (was mixing stderr into stdout)
  - Keep stderr separate (goes to terminal)
  - JSON parsing now works correctly
- **[P1] Fixed Amadeus CLI error handling**
  - Return None on errors (auth, network, API failures)
  - Return [] only for valid empty results
  - Exit with non-zero code on failures
  - Scripts can now detect real errors vs no results
- **[P2] Fixed AviationStack CLI error handling**
  - Return None on HTTP/timeout/connection errors
  - Return [] only for valid empty results
  - Exit with non-zero code on failures
  - No false "no flights" outcomes
- **[P2] Fixed ambiguous None in flight status responses**
  - `_parse_flight_data()` now returns {} for valid "not found"
  - `_parse_flight_data()` returns None only for errors
  - CLI distinguishes "not found" from network/API failures
  - Better error messages with helpful hints
- **[P2] Fixed Amadeus 400 responses returning exit 0**
  - HTTP 400 now returns None (error) instead of [] (empty)
  - Invalid dates/airport codes detected by automation
  - Helpful error messages with suggestions
  - Exit code 1 on bad requests
- **[P2] Fixed Amadeus config validation**
  - Added config structure validation (missing keys, sections)
  - Friendly error messages instead of KeyError tracebacks
  - Hints to check config.example.json
  - Consistent with AviationStack validation
- **[P2] Fixed AviationStack api_key validation**
  - Validate api_key exists and is non-empty before client creation
  - Friendly error messages instead of KeyError tracebacks
  - Consistent with Amadeus validation
  - Better user experience for partial configs

### 📚 Documentation
- Added `SECURITY.md` - Security audit and fixes
- Added `CONFIGURATION.md` - Complete configuration guide
- Added `PRICING.md` - Amadeus pricing explanation
- Added `WARNINGS.md` - Important warnings about API keys
- Added `config.example.json` - Example configuration file
- Updated `README.md` with better examples
- Updated `SKILL.md` with production pricing info

### 🐛 Fixed
- Corrected Amadeus pricing documentation (Production has FREE tier!)
- Added both test and production URLs to config.json
- Updated Python client to read URLs from config
- Fixed config.json structure (was missing production URLs)

### 🔧 Changed
- Removed hardcoded API URLs from Python client
- Made configuration more flexible
- Improved error handling
- Better argument validation

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| **1.0.0** | 2026-03-01 | Initial release with security fixes |

---

## Upgrade Guide

### From Pre-release to 1.0.0

**1. Update scripts:**
```bash
# Replace old scripts with new secure versions
cp scripts/search_flights.sh scripts/search_flights.sh.old
cp scripts/check_status.sh scripts/check_status.sh.old
# Download new versions
```

**2. Update config.json:**
```json
{
  "apis": {
    "amadeus": {
      "base_url_test": "https://test.api.amadeus.com",
      "base_url_production": "https://api.amadeus.com",
      "sandbox_mode": true
    }
  }
}
```

**3. Verify security:**
```bash
# Test with malicious input (should be safe now)
./scripts/search_flights.sh "CNF; echo INJECTED" "BKK" "2026-12-15"
# Should search for "CNF; echo INJECTED" as literal airport code
# Should NOT execute echo command
```

---

## Roadmap

### v1.1.0 (Planned)
- [ ] Hotel search integration
- [ ] Multi-city trip planning
- [ ] Price history charts
- [ ] Email notifications

### v1.2.0 (Future)
- [ ] More API providers (Duffel, Skyscanner)
- [ ] Machine learning price predictions

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
