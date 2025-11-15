# Authors can't express fallbacks for potentially-broken links

I work on the Google Chrome team, but no particular team under Chrome has expressed interest in this idea.
I also probably won't find time to fully flesh it out.
If anyone wants to pick it up and run with it, please do.
I have <em>not</em> licked this cookie.

## Proponents

- [Jeffrey Yasskin](https://jeffrey.yasskin.info/)

## Participate
- https://github.com/jyasskin/handling-unreliable-links/issues

## Table of Contents

<!-- Update this table of contents by running `npx doctoc README.md` -->
<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [Introduction](#introduction)
- [Goals](#goals)
- [Non-goals](#non-goals)
- [User research](#user-research)
- [User needs](#user-needs)
  - [Broken links](#broken-links)
  - [Hijacked links](#hijacked-links)
  - [Extractive paywalls or ads](#extractive-paywalls-or-ads)
  - [Unsupported link schemes](#unsupported-link-schemes)
  - [Unsupported resource types](#unsupported-resource-types)
- [Prior art](#prior-art)
  - [Robust links](#robust-links)
- [Alternatives Considered](#alternatives-considered)
  - [A list of fallback URLs to try in sequence](#a-list-of-fallback-urls-to-try-in-sequence)
  - [How this solution would solve the use cases](#how-this-solution-would-solve-the-use-cases)
- [Detailed design discussion](#detailed-design-discussion)
- [Security and Privacy Considerations](#security-and-privacy-considerations)
- [Stakeholder Feedback / Opposition](#stakeholder-feedback--opposition)
- [References & acknowledgements](#references--acknowledgements)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Introduction

Sometimes URLs break.

Sometimes certain users couldn't load a particular link in the first place.

Sometimes the author of a link knows of fallbacks that could work if the original link doesn't.

Authors can provide several adjacent links to allow users to handle broken ones,
but this takes up space on the page and requires the user to go back and click again.

## Goals

Let link sources make their links more reliable,
without requiring action by the link target.

I feel like the top user needs are
[broken links](#broken-links) and [unsupported link schemes](#unsupported-link-schemes),
but that's debatable.

## Non-goals

Unknown yet.

## User research

None yet. Plausibly we could search the HTTP Archive for how often developers render their own fallback links.

## User needs

### Broken links

A page links to a URL. A couple years later, the URL doesn't load anymore.
Users who follow the link encounter a 404.

### Hijacked links

A page links to a URL.
A couple years later,
the original author has lost control of the DNS entry for the domain,
and the URL points to spam.

### Extractive paywalls or ads

A page links to a URL, but when loading the URL,
the user is confronted with an overly-expensive paywall or way too many ads.
When current pages want to link to such a page,
they often just link to an archive instead,
which unconditionally removes support from the target page.
It would be better if they could link to the primary URL
but give the user the option of seeing the content if the target page is too expensive.

### Unsupported link schemes

A page links to a URL, but a user's browser doesn't understand the URL.

For example, a link might point to an `irc:...` URL
when the user doesn't have an IRC client installed.
Or a link might point to a [`fedi:...`](https://github.com/timbray/fedi-uri) URL
when the user doesn't have a Fediverse account.

[Paul Kinlan](https://paul.kinlan.me/detecting-if-a-url-scheme-can-be-handled/) and
[James Henstridge](https://theblower.au/@jamesh/109376597447099245) have described
a way to redirect from a fallback resource to the potentially-unsupported scheme.

### Unsupported resource types

A page links to a URL, but a user's browser doesn't understand the resource at the other end.

For example, a link might point to a JPEG-XL file when the user's browser doesn't support that format.
Resource negotiation could handle this, but only if the target server implements negotiation.

## Prior art

### [Robust links](https://robustlinks.org/spec/)

This is based on a set of `data-` attributes that
capture the notion of linking to the state of a page as of a certain time.
It can describe a set of archives, including the time that each archive was created.
If the link is created after the archived URL has changed,
it can record the original URL but still make the primary link visit an archive.

As far as I know, there's not yet an implementation of this design.

## Alternatives Considered

### A list of fallback URLs to try in sequence

```html
<a href="https://primary.example/resource/url"
   fallbackhref="https://web.archive.example/web/20251115095422/https://primary.example/resource/url
                 https://archive.example/AbcDe">Link text</a>
```

If the primary URL fails to load, or the user marks it unacceptable through some UI,
the UA should try to navigate to each fallback in sequence.

### How this solution would solve the use cases

This directly solves the broken link, unsupported scheme, and unsupported resource user needs.
It only solves the hijacked URL, and extractive page user needs if
the user notices the problem and the UA provides a discoverable button to fall back to the next URL.

This design doesn't identify the date at which the archives were created,
but it's not clear that most users need to take advantage of that data.

## Detailed design discussion

## Security and Privacy Considerations

TBD

## Stakeholder Feedback / Opposition

[Implementors and other stakeholders may already have publicly stated positions on this work. If you can, list them here with links to evidence as appropriate.]

- [Implementor A] : Positive
- [Stakeholder B] : No signals
- [Implementor C] : Negative

[If appropriate, explain the reasons given by other implementors for their concerns.]

## References & acknowledgements

- Thanks to [Herbert Van de Sompel](https://w3c.social/@hvdsomp) for
  [pointing out the Robust Links proposal](https://w3c.social/@hvdsomp/115552597811093015).