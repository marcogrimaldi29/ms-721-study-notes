# Contributing to MS-721 Study Notes

Thanks for helping keep these notes accurate. They cover **Microsoft Exam MS-721: Collaboration Communications Systems Engineer**, and Microsoft Teams changes fast: features get renamed or retired, settings move around the Teams admin center, and licensing shifts between one revision of the study guide and the next. Reports from people studying with the notes are the best way to catch that.

> [!IMPORTANT]
> **This repo takes issues, not pull requests.** Pull requests are turned off: I write every change myself and check it against the official documentation, so the notes keep one voice and stay consistent from page to page. If you know exactly how something should read, put your proposed wording in the issue. It's just as useful.

---

## Contents

- [Ways to Help](#-ways-to-help)
- [Before You Open an Issue](#-before-you-open-an-issue)
- [Opening an Issue](#-opening-an-issue)
- [Out of Scope](#-out-of-scope)
- [What Happens Next](#-what-happens-next)
- [Code of Conduct](#-code-of-conduct)
- [Security](#-security)

## 🙌 Ways to Help

Every report fits one of the five types in the [issue template](ISSUE_TEMPLATE.md):

| Type | What it covers | For example |
| --- | --- | --- |
| 🐛 **Bug** | A broken link, diagram, search or navigation | A Mermaid call-flow diagram doesn't render, a link to Microsoft Learn returns 404, or search can't find a term that's on the page |
| ❌ **Incorrect content** | Text, a table, a callout, a diagram or a PowerShell example is wrong | A cmdlet or parameter that doesn't exist, or a feature listed under the wrong Teams Rooms license (Basic or Pro) |
| 📅 **Outdated content** | Teams, the Teams admin center or the exam has changed since the page was written | A feature was renamed or retired, a setting has moved in the Teams admin center, or the study guide was revised after October 29, 2026 and lists a skill the notes don't cover |
| 📱 **Device or display issue** | Something looks wrong or doesn't work on a specific device, browser or screen size | A wide policy comparison table is cut off on a phone, or the navigation menu won't close on a tablet |
| ❓ **Other** | Anything else, including ideas to make the notes easier to study | A call flow that would read better as a diagram, a comparison table worth adding, or another exam caveat |

## 🔎 Before You Open an Issue

- **Search the existing issues**, [open and closed](https://github.com/marcogrimaldi29/ms-721-study-notes/issues?q=is%3Aissue). If yours is already there, add a 👍 reaction or a comment with anything new rather than opening a duplicate.
- **Check the date.** The notes are written against the **skills measured as of October 29, 2026**. If the "Skills measured as of" heading in the [study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ms-721) shows a newer date, say so in the issue — it tells me which revision to compare against. The guide's **Change log** section lists what moved between revisions.
- **Check the current docs.** A quick look at the [Microsoft Teams admin documentation](https://learn.microsoft.com/en-us/MicrosoftTeams/) usually confirms whether something has changed, and gives you the link the issue asks for.
- **One problem per issue.** Separate issues are easier to track, fix and close. Several typos on the same page can go in one issue.
- **For a site bug, hard-refresh first** (Ctrl+F5, or Cmd+Shift+R on a Mac) in case your browser is showing an older copy of the page.

## 📝 Opening an Issue

1. [Open a new issue](https://github.com/marcogrimaldi29/ms-721-study-notes/issues/new). The description box opens with the issue template already in it.
2. Give it a title that names the page and the problem, for example *"04 — Teams Rooms & Devices: Basic vs. Pro table is missing a feature"* rather than *"Mistake on page"*.
3. Tick one type of report, keep the page it's about, and fill in the sections that apply. Delete the rest.

> [!TIP]
> To link straight to a section, hover over its heading and click the link icon that appears beside it, then copy the address from your browser. On a phone, the section heading on its own is enough.

### What Makes a Report Easy to Fix

- **A link to the exact section**, not just the page
- **The text as the notes have it**, quoted, so it can be found even if the page has moved on
- **An official source** for any content change, and the date you checked it. Microsoft Learn is best. A report without a source may take longer, because every change has to be checked against the documentation before it goes in
- **For bugs and device issues:** the steps to reproduce it, what you expected and what happened instead, the device, operating system and browser, and a screenshot if it shows the problem
- **Your proposed wording**, if you have it. With pull requests turned off, this is the closest thing to suggesting an edit directly

## 🚫 Out of Scope

To keep the notes accurate and the issue tracker useful, please don't open issues for:

- **Live exam content.** Questions, answers or case studies remembered from a real exam break [Microsoft's exam security policy](https://learn.microsoft.com/en-us/credentials/support/exam-and-assessment-lab-security-policies). Issues or comments that include them will be closed and the content removed.
- **Exam booking, scores or retakes.** [Microsoft Credentials support](https://learn.microsoft.com/en-us/credentials/support/help) can help with those.
- **Help with your own Teams tenant or devices.** For troubleshooting a Direct Routing SBC, a call queue, a Teams Rooms console or your own policies, ask on [Microsoft Q&A](https://learn.microsoft.com/en-us/answers/) or [open a support request](https://learn.microsoft.com/en-us/microsoft-365/admin/get-help-support) from the Microsoft 365 admin center.
- **Anything private.** Issues are public, so leave out tenant IDs, phone numbers, SBC addresses, emergency addresses and user or customer details, including in screenshots.
- **Unofficial sources.** Third-party practice tests, exam dump sites and AI chat answers aren't accepted as the source for a content change.
- **Promotion.** Links to courses, products or services, affiliate links and paid placements won't be added.
- **Security vulnerabilities.** Please [report these privately](https://github.com/marcogrimaldi29/ms-721-study-notes/security/advisories/new), as described in the [security policy](SECURITY.md), not in a public issue.

## 🔄 What Happens Next

- I maintain the notes on my own, in my spare time, so replies are best effort and there's no fixed response time.
- I check each report against the official documentation. If something needs clarifying, I'll ask on the issue, so keep an eye on your GitHub notifications.
- When a fix goes in, the issue is closed with a note or a link to the change. The live site updates shortly after.
- If nothing changes, the issue is closed with the reason, such as out of scope, can't be verified, or working as intended.
- An issue waiting on a reply may be closed after a while. Comment with the missing details and it can be reopened.

## 🤝 Code of Conduct

By opening, commenting on or reacting to issues here, you agree to follow the [Code of Conduct](CODE_OF_CONDUCT.md). Comments that are abusive, spam or share live exam content may be hidden, edited or locked.

## 🔒 Security

Found a security problem with the site or this repo? Please don't report it in a public issue. Use [Report a vulnerability](https://github.com/marcogrimaldi29/ms-721-study-notes/security/advisories/new) instead, so only you and I can see it. The [security policy](SECURITY.md) has the details.

---

If the notes helped you, the README's [💚 Support](https://github.com/marcogrimaldi29/ms-721-study-notes#-support) section has a couple of ways to show it. Thanks for helping make them better for the next MS-721 candidate.
