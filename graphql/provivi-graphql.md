# Provivi Content GraphQL API

**Endpoint:** `https://api.provivi.com/admin/api`
**Schema:** [`provivi-content-schema.graphql`](provivi-content-schema.graphql) (SDL, 190KB)
**Retrieved:** 2026-08-26 by anonymous introspection (HTTP 200)
**Method:** probed

## What this is

This is the KeystoneJS 5 headless-CMS backend that serves the `provivi.com` marketing site. It is the
only machine-readable API surface Provivi operates. It is **undocumented**: Provivi publishes no
developer portal, no API reference, no getting-started guide, no terms of use for machine access, no
pricing and no support channel for it. It is captured here as an *observed public surface*, not as a
developer product Provivi offers.

## Shape

| | |
|---|---|
| Types | 199 |
| Query fields | 83 (20 content lists x `all<List>` / `<List>` / `_all<List>Meta` / `_<List>Meta`) |
| Mutation fields | 122 (create / update / delete, singular and bulk, per list) |
| Subscriptions | none |
| Lists | Article, ContactBannerBlock, Content, Department, Faq, Farmer, Goal, HeroBlock, Investor, Lang, Multimedia, Page, PressRelease, Product, Purpose, TeamMember, TeamMemberCountry, TeamMemberPosition, User, Vimeo |

Server stack, from response headers: `Apache/2.4.58 (Amazon Linux)` in front of `Express` /
Apollo Server, `X-Keystone-App-Version: 1.0.0`.

## Access observed

- **Introspection:** enabled, anonymous, HTTP 200.
- **Reads:** anonymous, HTTP 200. `{ _allProductsMeta { count } _allArticlesMeta { count } _allPagesMeta { count } }`
  returned `2`, `97` and `55` respectively on 2026-08-26 — public website content.
- **Session:** `{ authenticatedUser { id } }` returns `null` anonymously. Keystone session auth
  (`authenticateUserWithPassword`) is declared in the schema.
- **Writes:** the 122 mutation fields were **not** exercised. This pipeline never executes write
  operations against a provider, so whether Keystone access control gates them is unknown and is not
  asserted either way. The `allUsers` query was likewise **not** run, to avoid touching personal data.
- **CORS:** `Access-Control-Allow-Origin: *` with `Access-Control-Allow-Credentials: true`.

## Notes for the provider

Two things worth knowing, offered as findings rather than judgements:

1. **Production error responses leak server paths.** A plain validation error returns
   `extensions.exception.stacktrace` containing absolute filesystem paths
   (`/home/ec2-user/provivi/apis/node_modules/...`), which indicates Apollo debug output is on in
   production.
2. **The customer-app link is dead.** `provivi-app.com`, linked from the provivi.com footer, returned
   NXDOMAIN on 2026-08-26.
