# PostHog setup report

## Summary

The wizard created a PostHog analytics dashboard and recorded a ten-event portfolio interaction plan, but did not add a live SDK integration because this repository is a Zola static site rather than an Astro application.

## What was installed and initialized

- **SDK installation:** None. The run found no package manifest, package manager, lockfile, or package scripts, so no PostHog SDK dependency was declared or installed.
- **Initialization:** None. No browser SDK, `window.posthog` client, Astro component, server-side API route, or environment configuration was added.
- **Runtime verification:** No event arrival was observed. The passing review concerns scope and compatibility only; it does not prove that events flow.

## Planned events

These are planned event definitions recorded by the run, not confirmed captures:

| Event | What it measures | File |
|---|---|---|
| `search_opened` | Visitor opens the site search interface. | `static/search-fuse.js` |
| `theme_selected` | Visitor explicitly selects a light, dark, or system theme. | `static/theme-switcher.js` |
| `code_copied` | Visitor copies a code example from an article. | `static/copy-button.js` |
| `comments_loaded` | Visitor loads federated comments for an article. | `static/comments.js` |
| `share_clicked` | Visitor opens the share action for an article. | `templates/article.html` |
| `issue_tracker_opened` | Visitor opens the issue tracker from an article. | `templates/article.html` |
| `article_navigation_clicked` | Visitor follows previous or next article navigation. | `templates/article.html` |
| `navigation_link_clicked` | Visitor follows a primary navigation link. | `templates/partials/nav.html` |
| `social_profile_opened` | Visitor opens a social profile from the site footer. | `templates/partials/footer.html` |
| `external_post_opened` | Visitor opens the original federated post to comment. | `templates/partials/comments.html` |

The capture step explicitly reported that no application capture calls were added. The files above are therefore event-plan targets, not instrumented call sites.

## User identification

Identification was **skipped**. The site has no login, registration, logout, authentication session, API endpoint, or client-side user state. Editorial author metadata is not an application identity and was correctly not used as a distinct ID. If authentication is added later, identify after successful authentication with a stable non-PII account identifier and reset on logout.

## Error tracking

No error tracking was added. The requested Astro error-boundary mechanism does not apply to this Zola site, which has no Astro entrypoint, JavaScript SDK runtime, or framework error boundary.

## Dashboard

The dashboard **Analytics basics (wizard)** was created with five insights covering portfolio interactions, theme selections, article engagement, navigation/social discovery, and a content interaction funnel. The definitions use the planned event names and may be empty until compatible site instrumentation is implemented and deployed.

[Open the Analytics basics dashboard](https://us.posthog.com/project/525525/dashboard/1895338)

## Unresolved issues and cost

1. **Framework mismatch:** The requested Astro hybrid integration cannot be applied to this Zola repository. Leaving it unresolved means no PostHog SDK, capture calls, identification, or error tracking will run in the current site.
2. **Deployment/configuration path:** A future Zola implementation must establish how the real PostHog configuration is injected at deployment time. The existing CSP also needs a compatible approach: the recorded handoff says `script-src` is `'self'` and `connect-src` currently allows only `https://toot.community`. Leaving this unresolved can block the SDK or event connections.
3. **No stable identity:** There is no authenticated identity to attribute events to. Leaving this unresolved is appropriate for the current anonymous portfolio, but any future authenticated flow would otherwise fragment activity across anonymous IDs.

## Build conflict

No package manifest, SDK declaration, or defined package build, lint, or typecheck command exists. The build task was therefore marked not needed. No build or lint validation was run, and no passing build should be inferred. The repository remains a Zola static site without a browser SDK integration.

## Next steps

1. Create a separate Zola/static-site integration plan rather than introducing Astro files or package-based Astro APIs.
2. Choose a deployment-time configuration and browser SDK delivery strategy that works with the site's CSP.
3. Add guarded capture calls at the ten planned interaction targets without putting PII in event properties.
4. Deploy the site, exercise each interaction, and verify the corresponding events arrive in PostHog.
5. Confirm the dashboard populates after verified event ingestion.

## Before you merge

- [ ] `static/search-fuse.js` — inspect the planned search interaction call site (line number was not recorded by this run) after a Zola-compatible SDK is added; exercise it and confirm `search_opened` arrives.
- [ ] `static/theme-switcher.js` — inspect the planned theme-selection call site (line number was not recorded by this run); exercise each theme path and confirm `theme_selected` arrives.
- [ ] `static/copy-button.js` — inspect the planned code-copy call site (line number was not recorded by this run); confirm `code_copied` arrives.
- [ ] `static/comments.js` — inspect the planned comments-loading call site (line number was not recorded by this run); confirm `comments_loaded` arrives.
- [ ] `templates/article.html` — inspect the planned share, issue-tracker, and article-navigation call sites (line numbers were not recorded by this run); confirm all three events arrive.
- [ ] `templates/partials/nav.html` — inspect the planned primary-navigation call site (line number was not recorded by this run); confirm `navigation_link_clicked` arrives.
- [ ] `templates/partials/footer.html` — inspect the planned social-profile call site (line number was not recorded by this run); confirm `social_profile_opened` arrives.
- [ ] `templates/partials/comments.html` — inspect the planned federated-post call site (line number was not recorded by this run); confirm `external_post_opened` arrives.
- [ ] `config.toml` / `templates/partials/csp.html` — verify the final CSP allows the chosen SDK and event endpoint, then check the browser console for CSP violations (line numbers were not recorded by this run).
- [ ] Run the site's full production build and test suite once a compatible integration exists; this run had no project-defined commands and did not validate either.
