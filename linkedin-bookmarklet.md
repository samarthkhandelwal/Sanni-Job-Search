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
javascript:(function(){var T=function(el){return el?(el.textContent||'').trim().replace(/\s+/g,' '):'';};var $=function(s,r){return(r||document).querySelector(s);};var title='',company='',location='',description='',url=window.location.href.split('?')[0];try{var scripts=document.querySelectorAll('script[type="application/ld+json"]');for(var i=0;i<scripts.length;i++){try{var data=JSON.parse(scripts[i].textContent);var arr=Array.isArray(data)?data:[data];for(var j=0;j<arr.length;j++){var d=arr[j];if(d&&d['@type']==='JobPosting'){title=title||d.title||'';company=company||(d.hiringOrganization&&d.hiringOrganization.name)||'';if(d.jobLocation){var locs=Array.isArray(d.jobLocation)?d.jobLocation:[d.jobLocation];var locParts=locs.map(function(l){var addr=(l&&l.address)||{};return[addr.addressLocality,addr.addressRegion,addr.addressCountry].filter(Boolean).join(', ');}).filter(Boolean);location=location||locParts.join(' · ');}description=description||(d.description||'').replace(/<[^>]+>/g,' ').replace(/&nbsp;/g,' ').replace(/&amp;/g,'&').replace(/&lt;/g,'<').replace(/&gt;/g,'>').replace(/\s+/g,' ').trim().slice(0,6000);}}}catch(e){}}}catch(e){}if(!title||!company){var og=function(p){var e=$('meta[property="'+p+'"]')||$('meta[name="'+p+'"]');return e?e.content:'';};var ogTitle=og('og:title');var dt=document.title||'';var srcs=[ogTitle,dt];for(var k=0;k<srcs.length;k++){var s=srcs[k];var m=s.match(/^(.+?)\s+hiring\s+(.+?)\s+in\s+(.+?)\s*[|·]/i);if(m){company=company||m[1].trim();title=title||m[2].trim();location=location||m[3].trim();break;}}if(!description)description=(og('og:description')||'').slice(0,6000);}if(!title){title=T($('h1.t-24')||$('.job-details-jobs-unified-top-card__job-title')||$('.jobs-unified-top-card__job-title')||$('main h1')||$('h1'));}if(!company){company=T($('a[href*="/company/"]'));}if(!location){location=T($('.job-details-jobs-unified-top-card__primary-description-container .tvm__text')||$('.tvm__text--low-emphasis')||$('.jobs-unified-top-card__bullet'));}if(!description){description=T($('#job-details')||$('.jobs-description__content')||$('.jobs-description-content__text')||$('article')).slice(0,6000);}if(!title||!company){alert('Could not detect LinkedIn job info.\n\nTry:\n1. Open the job by clicking its title to get a direct URL like linkedin.com/jobs/view/12345\n2. Refresh the page (Cmd-R) and try again\n3. If it still fails, ping Samarth\n\nDebug: title='+(title||'(none)')+' company='+(company||'(none)'));return;}var job={title:title,company:company,location:location,description:description,url:url};var encoded=encodeURIComponent(btoa(unescape(encodeURIComponent(JSON.stringify(job)))));var DASHBOARD='YOUR_DASHBOARD_URL_HERE';window.open(DASHBOARD.replace(/\/$/,'')+'/#linkedin='+encoded,'_blank');})();
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
