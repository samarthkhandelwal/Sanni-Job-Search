# LinkedIn → Dashboard Bookmarklet

A tiny one-click button that pulls any LinkedIn job into Sanni's dashboard with the full Apply Pack (cover letter, tailored resume, recruiter outreach prompts) ready to go.

## How it works

1. Sanni is on `linkedin.com/jobs/view/12345...` viewing a job posting (logged in normally).
2. She clicks the **Save to Sanni Dashboard** bookmarklet on her bookmarks bar.
3. The bookmarklet reads the job's title, company, location, and full JD from the page DOM (no scraping bot — just reading what's already rendered for her eyes).
4. It opens a new tab at her hosted dashboard URL with the job data encoded in the URL fragment.
5. The dashboard unlocks (password cached in sessionStorage if she's already used it that session), the **Prep modal pops automatically** with the LinkedIn job pre-loaded.
6. From there she can copy any of the 3 prompts (cover letter / tailored resume / recruiter outreach), or hit **+ Mark as applied** to push it into her tracker.

No LinkedIn account risk — the bookmarklet does not run on a schedule, does not scrape multiple pages, does not bypass auth, does not interact with LinkedIn's API. It reads what's already on the page Sanni is looking at, triggered by her own click. Same model as Pocket / Trello web clipper.

## Setup (one-time, ~2 minutes)

### Step 1 — Get the bookmarklet code

The full bookmarklet is below as one long line. **You must first replace `YOUR_DASHBOARD_URL_HERE` with the actual hosted URL.** For example, if your GitHub Pages URL is `https://samarthk.github.io/sanni-jobs/`, replace that token with that URL (no trailing slash needed but harmless).

```text
javascript:(function(){var $=function(s,r){return(r||document).querySelector(s);};var $A=function(s,r){return Array.from((r||document).querySelectorAll(s));};var T=function(el){return el?(el.textContent||'').trim().replace(/\s+/g,' '):'';};var pickFirst=function(sels,root){for(var i=0;i<sels.length;i++){var el=$(sels[i],root);if(el)return el;}return null;};var container=pickFirst(['.jobs-search__job-details','.jobs-search__job-details--container','.jobs-details__main-content','.job-view-layout','.jobs-details','main'])||document;var titleEl=pickFirst(['.job-details-jobs-unified-top-card__job-title h1','.jobs-unified-top-card__job-title h1','.job-details-jobs-unified-top-card__job-title','.jobs-unified-top-card__job-title','h1.t-24','.topcard__title','[data-test-id="job-title"]','h1'],container);var title=T(titleEl);var companyEl=pickFirst(['.job-details-jobs-unified-top-card__company-name a','.jobs-unified-top-card__company-name a','.job-details-jobs-unified-top-card__company-name','.jobs-unified-top-card__company-name','.topcard__org-name-link','.jobs-poster__company-link','[data-test-id="job-poster"]','a[data-tracking-control-name*="company"]','a[href*="/company/"]'],container);var company=T(companyEl);if(!title||!company){var dt=document.title||'';var m=dt.match(/^(.+?)\\s+hiring\\s+(.+?)\\s+in\\s+(.+?)\\s*[\\|·]/i);if(m){company=company||m[1].trim();title=title||m[2].trim();}else{var m2=dt.match(/^(.+?)\\s+[-|·]\\s+(.+?)\\s+[-|·]/);if(m2&&!title){title=m2[1].trim();}}}var locEl=pickFirst(['.job-details-jobs-unified-top-card__primary-description-container .tvm__text','.job-details-jobs-unified-top-card__primary-description-container span','.jobs-unified-top-card__bullet','.tvm__text--low-emphasis','.topcard__flavor--bullet','[data-test-id="job-location"]'],container);var location=T(locEl);var descEl=pickFirst(['#job-details','.jobs-description-content__text','.jobs-description__container','.jobs-description__content','.jobs-box__html-content','article.jobs-description','[class*="jobs-description"]','article']);var description=T(descEl).slice(0,6000);var url=window.location.href.split('?')[0];if(!title||!company){alert('Could not detect LinkedIn job info.\\n\\nTry one of these:\\n\\n1. Open the job by clicking its title to get a URL like linkedin.com/jobs/view/12345\\n\\n2. If on the search/recommended page, click a job in the left list, wait 2s for the right panel to load, then click the bookmark\\n\\n3. If LinkedIn changed their layout (they do every few months), tell Samarth and he can update the selectors');return;}var job={title:title,company:company,location:location,description:description,url:url};var encoded=encodeURIComponent(btoa(unescape(encodeURIComponent(JSON.stringify(job)))));var DASHBOARD='YOUR_DASHBOARD_URL_HERE';window.open(DASHBOARD.replace(/\/$/,'')+'/#linkedin='+encoded,'_blank');})();
```

### Step 2 — Install on Sanni's browser

1. Make sure the bookmarks bar is visible (Chrome: Cmd-Shift-B / Ctrl-Shift-B).
2. Right-click the bookmarks bar → **Add page** (or **Add bookmark**).
3. **Name**: `Save to Sanni Dashboard`
4. **URL**: paste the entire `javascript:...` line above (with your real dashboard URL substituted).
5. Save.

That's it. The bookmarklet is now a clickable button on her bookmarks bar.

### Step 3 — Test it

1. Open any LinkedIn job posting (`linkedin.com/jobs/view/...` or click a job in the search results).
2. Click the **Save to Sanni Dashboard** bookmark.
3. New tab opens to the dashboard. If she's never unlocked it in this session, the password gate appears — enter `kale-jobs-2026`.
4. After unlock, the Prep modal should auto-pop with the LinkedIn job's title, company, location, and JD pre-filled.
5. Try the **📝 Cover Letter + Short Answers** button — it should produce a complete prompt referencing the LinkedIn JD.

## Quick troubleshooting

| Symptom | Fix |
|---|---|
| Alert "Could not detect LinkedIn job info" | Make sure the full posting is visible on the right side of the screen (not just the list view). Click the job in the list first. |
| New tab opens but goes to wrong URL | The `YOUR_DASHBOARD_URL_HERE` placeholder wasn't replaced — edit the bookmark, fix the URL. |
| Modal doesn't auto-open | Hard-reload the dashboard once (Cmd-Shift-R), then try the bookmarklet again. |
| Wrong company/title detected | LinkedIn rebuilt their job-page DOM. Ping Samarth to update the selectors in the bookmarklet — takes 2 minutes. |

## When LinkedIn changes their HTML

LinkedIn rebuilds their job-page UI 2–3× per year and the selectors break. When that happens, the bookmarklet might detect nothing or detect the wrong fields. The fix is updating the CSS selectors in the bookmarklet (the chain of `$('...')||$('...')||...` fallbacks). Easy 5-minute job — just ask Samarth and he can update + share a new bookmarklet line.
