# Changelog

All notable changes to the GetJohn Vendor Version Checker module are documented here.

## v1.2.0 — 2026-02-11

### New Features

- **Private Composer repository support** — Auto-detects private repos from `composer.json` repository definitions and authenticates using `auth.json` credentials. Queries private Satis/Composer repos with HTTP basic auth to resolve latest stable versions.
- **Multi-format Satis resolution** — Supports V2 (`p2/`), V1 (`p/`), full `packages.json`, and Satis provider-includes with hash-based URL resolution.
- **Private Repo source badge** — Dashboard and report now display `[via Private Repo]` for versions resolved from private Composer repos.
- **Auth expired detection** — When private repo queries fail, the report shows "auth may be expired" with clear guidance instead of generic error messages.

### Improvements

- **Smart repo filtering** — Skips `repo.magento.com`, `marketplace.magento.com`, and internal Satis/GetJohn repos to avoid matching Magento core packages as private repo candidates.
- **XTENTO private repo working** — Successfully resolves versions for `xtento/pdfcustomizer` (2.14.9 → 2.17.0) and `xtento/xtcore` (2.17.0 → 2.21.0) via `dowlis.repo.xtento.com`.
- **Dashboard updated** — HTML dashboard reflects all v1.2.0 findings: Private Repo vendor column, updated Known Limitations with Amasty key migration notes, auth expired status in Notes column.
- **Renamed branding** — All "Cisco Merchandise Store" references updated to "Dowlis Merchandise Store".

### Known Limitations

- **Amasty** — Private repo (`composer.amasty.com`) returns 403. Global Composer keys deprecated 1 Jan 2026. Requires project-level keys from Amasty customer portal.
- **MageWorx** — Private repo (`packages.mageworx.com`) credentials present but returning errors. Auth may be expired.
- **Aheadworks** — Private repo (`dist.aheadworks.com`) credentials present but repo format not yet verified.

---

## v1.1.0 — 2026-02-11

### Bug Fixes

- **Fixed `getSupportedVendors()` return format** — The method in `ComposerIntegration` was incorrectly transforming vendor keys using `explode('.', $domain)[0]`, causing vendor name mismatches and preventing packages from being matched. Removed the unnecessary transformation so vendor keys are passed through directly from `VersionChecker`.
- **Fixed XTENTO version extraction picking Magento 1 version** — The XTENTO product pages list both M1 and M2 versions. The previous regex `/Version:?\s*(\d+\.\d+\.\d+)/i` matched the first occurrence (M1). Implemented a `version_select: highest` strategy with a case-sensitive pattern `/Version:\s*(\d+\.\d+\.\d+)/` that matches only the Developer Information version entries, then selects the highest (M2) version.

### New Features

- **Packagist API fallback** — When vendor website scraping fails (Cloudflare block, no version on page), the checker now falls back to querying the Packagist API (`repo.packagist.org/p2/`) for the latest stable version. Results from Packagist are clearly labelled `[via Packagist]` in the report.
- **Packagist-only package tracking** — Added support for packages that don't have vendor website patterns but are available on public Packagist. These are checked directly via the Packagist API without requiring website scraping.
- **UNAVAILABLE status** — New status category for packages where the vendor page loads successfully but no version information can be extracted. Previously these showed as `AHEAD_OF_VENDOR` with an empty version, which was misleading.
- **Cloudflare detection** — HTTP 403 responses containing Cloudflare challenge markers are now detected and reported with a clear "Cloudflare protection detected" message instead of dumping the raw error response.
- **Version source tracking** — Each result now includes a `source` field indicating whether the version was obtained from `vendor_website` or `packagist`. The report displays `[via Packagist]` for fallback results.

### Expanded Package Coverage

Added URL mappings and Packagist tracking for the Dowlis Merchandise Store project:

**New vendor website mappings:**
- `amasty/gdpr-cookie` — GDPR Cookie Compliance
- `amasty/geoipredirect` — GeoIP Redirect
- `amasty/module-gdpr` — GDPR
- `amasty/number` — Custom Order Number
- `aheadworks/module-blog` — Blog
- `mageplaza/module-smtp` — SMTP
- `bsscommerce/disable-compare` — Disable Compare (moved to Packagist)

**New Packagist-only packages:**
- `taxjar/module-taxjar`
- `webshopapps/module-matrixrate`
- `klaviyo/magento2-extension`
- `yotpo/magento2-module-yotpo-loyalty`
- `yotpo/module-review`
- `paradoxlabs/authnetcim`
- `paradoxlabs/tokenbase`
- `justuno.com/m2`
- `stripe/stripe-payments`
- `bsscommerce/disable-compare`
- `mageworx/module-donationsmeta`
- `mageworx/module-giftcards`

### Technical Improvements

- **Updated User-Agent** — Changed from custom `MagentoVersionChecker/1.0` to a standard Chrome browser User-Agent to reduce bot detection.
- **`section_filter` support in `extractVersion()`** — New vendor pattern option to isolate HTML sections before version extraction (used internally for future vendor patterns).
- **`version_select` support in `extractVersion()`** — When set to `highest`, finds all version matches and returns the highest via `version_compare`. Used by XTENTO to correctly select M2 version over M1.

### Known Limitations

- **Amasty** — All Amasty product pages are protected by Cloudflare Managed Challenge v3, which requires JavaScript execution. Versions cannot be scraped. Not available on public Packagist.
- **Aheadworks** — Also behind Cloudflare protection. Not available on public Packagist.
- **MageWorx** — Product pages do not display version numbers. Not available on public Packagist.
- **Stripe** — `stripe/stripe-payments` (Magento module) is not published on public Packagist.

---

## v1.0.0

Initial release.

