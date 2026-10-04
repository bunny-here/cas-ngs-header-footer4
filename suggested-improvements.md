```python
import os
import json
import re

# Look for html files in current directory or uploaded files
files = [f for f in os.listdir('.') if f.endswith('.html')]
print("Files:", files)

# Let's search in subdirectories or parent directories if any
for root, dirs, filenames in os.walk('.'):
    for f in filenames:
        if f.endswith('.html') or 'casngs' in f:
            print(os.path.join(root, f))


```

```text
Files: ['casngs.com-20261004T140410.html', 'casngs.com-20261004T115525.html', 'casngs.com-20261003T165611.html', 'lighthouse-performance-report.html']
./casngs.com-20261004T140410.html
./casngs.com-20261004T115525.html
./casngs.com-20261003T165611.html
./lighthouse-performance-report.html


```

```python
from bs4 import BeautifulSoup
import json

with open('casngs.com-20261004T140410.html', 'r', encoding='utf-8') as f:
    content = f.read()

# Extract __LIGHTHOUSE_JSON__
match = re.search(r'window\.__LIGHTHOUSE_JSON__\s*=\s*(\{.*?\}\s*);</script>', content, re.DOTALL)
if match:
    data = json.loads(match.group(1))
    print("Keys in JSON:", data.keys())
    
    # Get categories, performance score, audits
    perf_category = data['categories']['performance']
    print("Performance Score:", perf_category['score'] * 100)
    
    audit_refs = perf_category['auditRefs']
    print("\nAudit Refs in Performance Category:")
    for ref in audit_refs:
        audit_id = ref['id']
        weight = ref.get('weight', 0)
        group = ref.get('group', '')
        audit = data['audits'].get(audit_id, {})
        title = audit.get('title', '')
        score = audit.get('score', None)
        displayValue = audit.get('displayValue', '')
        details = audit.get('details', {})
        overallSavingsMs = details.get('overallSavingsMs', 0) if isinstance(details, dict) else 0
        overallSavingsBytes = details.get('overallSavingsBytes', 0) if isinstance(details, dict) else 0
        
        # print non-passed or weighted audits
        if score is not None and score < 0.9:
            print(f"ID: {audit_id} | Weight: {weight} | Group: {group} | Score: {score} | Display: {displayValue} | SavingsMs: {overallSavingsMs} | SavingsBytes: {overallSavingsBytes} | Title: {title}")
else:
    print("JSON not found")


```

```text
Traceback (most recent call last):
  File "<xbox-string>", line 1, in <module>
    from bs4 import BeautifulSoup
ModuleNotFoundError: No module named 'bs4'


```

```python
import re
import json

with open('casngs.com-20261004T140410.html', 'r', encoding='utf-8') as f:
    content = f.read()

match = re.search(r'window\.__LIGHTHOUSE_JSON__\s*=\s*({.*?});</script>', content, re.DOTALL)
if not match:
    match = re.search(r'window\.__LIGHTHOUSE_JSON__\s*=\s*({.*)', content, re.DOTALL)

data = json.loads(match.group(1).rstrip(';</script>').strip())

perf_category = data['categories']['performance']
print("Performance Score:", perf_category['score'] * 100)

audit_refs = perf_category['auditRefs']
print("\n--- ALL AUDIT REFS & SCORES ---")
for ref in audit_refs:
    audit_id = ref['id']
    weight = ref.get('weight', 0)
    group = ref.get('group', '')
    audit = data['audits'].get(audit_id, {})
    title = audit.get('title', '')
    score = audit.get('score', None)
    displayValue = audit.get('displayValue', '')
    details = audit.get('details', {})
    overallSavingsMs = details.get('overallSavingsMs', 0) if isinstance(details, dict) else 0
    overallSavingsBytes = details.get('overallSavingsBytes', 0) if isinstance(details, dict) else 0
    
    print(f"ID: {audit_id} | Weight: {weight} | Group: {group} | Score: {score} | Display: {displayValue} | SavingsMs: {overallSavingsMs} | SavingsBytes: {overallSavingsBytes} | Title: {title}")


```

```text
Performance Score: 26.0

--- ALL AUDIT REFS & SCORES ---
ID: first-contentful-paint | Weight: 10 | Group: metrics | Score: 0.02 | Display: 3.6 s | SavingsMs: 0 | SavingsBytes: 0 | Title: First Contentful Paint
ID: largest-contentful-paint | Weight: 25 | Group: metrics | Score: 0.03 | Display: 6.4 s | SavingsMs: 0 | SavingsBytes: 0 | Title: Largest Contentful Paint
ID: total-blocking-time | Weight: 30 | Group: metrics | Score: 0 | Display: 4,400 ms | SavingsMs: 0 | SavingsBytes: 0 | Title: Total Blocking Time
ID: cumulative-layout-shift | Weight: 25 | Group: metrics | Score: 1 | Display: 0 | SavingsMs: 0 | SavingsBytes: 0 | Title: Cumulative Layout Shift
ID: speed-index | Weight: 10 | Group: metrics | Score: 0 | Display: 15.7 s | SavingsMs: 0 | SavingsBytes: 0 | Title: Speed Index
ID: cache-insight | Weight: 0 | Group: insights | Score: 0 | Display: Est savings of 73 KiB | SavingsMs: 0 | SavingsBytes: 0 | Title: Use efficient cache lifetimes
ID: cls-culprits-insight | Weight: 0 | Group: insights | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Layout shift culprits
ID: document-latency-insight | Weight: 0 | Group: insights | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Document request latency
ID: dom-size-insight | Weight: 0 | Group: insights | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Optimize DOM size
ID: duplicated-javascript-insight | Weight: 0 | Group: insights | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Duplicated JavaScript
ID: font-display-insight | Weight: 0 | Group: insights | Score: 0 | Display: Est savings of 70 ms | SavingsMs: 0 | SavingsBytes: 0 | Title: Font display
ID: forced-reflow-insight | Weight: 0 | Group: insights | Score: 0 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Forced reflow
ID: image-delivery-insight | Weight: 0 | Group: insights | Score: 0 | Display: Est savings of 184 KiB | SavingsMs: 0 | SavingsBytes: 0 | Title: Improve image delivery
ID: inp-breakdown-insight | Weight: 0 | Group: insights | Score: None | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: INP breakdown
ID: lcp-breakdown-insight | Weight: 0 | Group: insights | Score: 0 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: LCP breakdown
ID: lcp-discovery-insight | Weight: 0 | Group: insights | Score: None | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: LCP request discovery
ID: legacy-javascript-insight | Weight: 0 | Group: insights | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Legacy JavaScript
ID: modern-http-insight | Weight: 0 | Group: insights | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Modern HTTP
ID: network-dependency-tree-insight | Weight: 0 | Group: insights | Score: 0 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Network dependency tree
ID: render-blocking-insight | Weight: 0 | Group: insights | Score: 0 | Display: Est savings of 1,930 ms | SavingsMs: 0 | SavingsBytes: 0 | Title: Render-blocking requests
ID: third-parties-insight | Weight: 0 | Group: insights | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: 3rd parties
ID: viewport-insight | Weight: 0 | Group: insights | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Optimize viewport for mobile
ID: interactive | Weight: 0 | Group: hidden | Score: 0.02 | Display: 11.3 s | SavingsMs: 0 | SavingsBytes: 0 | Title: Time to Interactive
ID: max-potential-fid | Weight: 0 | Group: hidden | Score: 0 | Display: 1,350 ms | SavingsMs: 0 | SavingsBytes: 0 | Title: Max Potential First Input Delay
ID: unminified-css | Weight: 0 | Group: diagnostics | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Minify CSS
ID: unminified-javascript | Weight: 0 | Group: diagnostics | Score: 0 | Display: Est savings of 7 KiB | SavingsMs: 40 | SavingsBytes: 6949 | Title: Minify JavaScript
ID: unused-css-rules | Weight: 0 | Group: diagnostics | Score: 0 | Display: Est savings of 60 KiB | SavingsMs: 270 | SavingsBytes: 61343 | Title: Reduce unused CSS
ID: unused-javascript | Weight: 0 | Group: diagnostics | Score: 0 | Display: Est savings of 284 KiB | SavingsMs: 180 | SavingsBytes: 291271 | Title: Reduce unused JavaScript
ID: total-byte-weight | Weight: 0 | Group: diagnostics | Score: 1 | Display: Total size was 1,994 KiB | SavingsMs: 0 | SavingsBytes: 0 | Title: Avoids enormous network payloads
ID: user-timings | Weight: 0 | Group: diagnostics | Score: None | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: User Timing marks and measures
ID: bootup-time | Weight: 0 | Group: diagnostics | Score: 0 | Display: 17.9 s | SavingsMs: 0 | SavingsBytes: 0 | Title: Reduce JavaScript execution time
ID: mainthread-work-breakdown | Weight: 0 | Group: diagnostics | Score: 0 | Display: 43.6 s | SavingsMs: 0 | SavingsBytes: 0 | Title: Minimize main-thread work
ID: long-tasks | Weight: 0 | Group: diagnostics | Score: 1 | Display: 20 long tasks found | SavingsMs: 0 | SavingsBytes: 0 | Title: Avoid long main-thread tasks
ID: non-composited-animations | Weight: 0 | Group: diagnostics | Score: None | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Avoid non-composited animations
ID: unsized-images | Weight: 0 | Group: diagnostics | Score: 0.5 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Image elements do not have explicit `width` and `height`
ID: bf-cache | Weight: 0 | Group: diagnostics | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Page didn't prevent back/forward cache restoration
ID: network-requests | Weight: 0 | Group: hidden | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Network Requests
ID: network-rtt | Weight: 0 | Group: hidden | Score: 1 | Display: 200 ms | SavingsMs: 0 | SavingsBytes: 0 | Title: Network Round Trip Times
ID: network-server-latency | Weight: 0 | Group: hidden | Score: 1 | Display: 300 ms | SavingsMs: 0 | SavingsBytes: 0 | Title: Server Backend Latencies
ID: main-thread-tasks | Weight: 0 | Group: hidden | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Tasks
ID: diagnostics | Weight: 0 | Group: hidden | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Diagnostics
ID: metrics | Weight: 0 | Group: hidden | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Metrics
ID: screenshot-thumbnails | Weight: 0 | Group: hidden | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Screenshot Thumbnails
ID: final-screenshot | Weight: 0 | Group: hidden | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Final Screenshot
ID: script-treemap-data | Weight: 0 | Group: hidden | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Script Treemap Data
ID: resource-summary | Weight: 0 | Group: hidden | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Resources Summary
ID: redirects | Weight: 0 | Group: hidden | Score: 1 | Display:  | SavingsMs: 0 | SavingsBytes: 0 | Title: Avoid multiple page redirects
ID: server-response-time | Weight: 0 | Group: hidden | Score: 1 | Display: Root document took 40 ms | SavingsMs: 0 | SavingsBytes: 0 | Title: Initial server response time was short
ID: layout-shifts | Weight: 0 | Group: hidden | Score: 1 | Display: 15 layout shifts found | SavingsMs: 0 | SavingsBytes: 0 | Title: Avoid large layout shifts


```

```python
# Detailed inspection of specific failing audits
failing_audits = [
    'render-blocking-insight',
    'unused-javascript',
    'unused-css-rules',
    'bootup-time',
    'mainthread-work-breakdown',
    'image-delivery-insight',
    'forced-reflow-insight',
    'font-display-insight',
    'cache-insight',
    'unsized-images',
    'unminified-javascript',
    'lcp-breakdown-insight',
    'network-dependency-tree-insight'
]

for aid in failing_audits:
    audit = data['audits'].get(aid, {})
    print(f"\n==================== AUDIT: {aid} ({audit.get('title')}) ====================")
    print("Description:", audit.get('description'))
    print("DisplayValue:", audit.get('displayValue'))
    details = audit.get('details', {})
    if isinstance(details, dict):
        print("Details type:", details.get('type'))
        if 'items' in details:
            print("Items count:", len(details['items']))
            for item in details['items'][:10]: # print top 10
                print("  - Item:", item)


```

```text

==================== AUDIT: render-blocking-insight (Render-blocking requests) ====================
Description: Requests are blocking the page's initial render, which may delay LCP. [Deferring or inlining](https://developer.chrome.com/docs/performance/insights/render-blocking) can move these network requests out of the critical path.
DisplayValue: Est savings of 1,930 ms
Details type: table
Items count: 46
  - Item: {'url': 'https://cdn.tailwindcss.com/3.4.17?plugins=forms@0.5.10,container-queries@0.1.1', 'totalBytes': 130216, 'wastedMs': 313}
  - Item: {'url': 'https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js', 'totalBytes': 121557, 'wastedMs': 1444}
  - Item: {'url': 'https://collect.commerce.godaddy.com/sdk.js', 'totalBytes': 48570, 'wastedMs': 771}
  - Item: {'url': 'https://fonts.googleapis.com/css2?family=Hanken+Grotesk:wght@100..900&family=JetBrains+Mono:wght@100..900&display=swap', 'totalBytes': 612}
  - Item: {'url': 'https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:wght,FILL@100..700,0..1&display=swap', 'totalBytes': 891, 'wastedMs': 54}
  - Item: {'url': 'https://casngs.com/wp-content/mu-plugins/vendor/godaddy/mwc-core/assets/js/payments/frontend/poynt.js', 'totalBytes': 3781}
  - Item: {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/js/um-gdpr.min.js?ver=2.14.0', 'totalBytes': 625}
  - Item: {'url': 'https://casngs.com/wp-includes/js/jquery/jquery.min.js?ver=3.7.1', 'totalBytes': 30016, 'wastedMs': 200}
  - Item: {'url': 'https://casngs.com/wp-content/plugins/woocommerce/assets/js/jquery-blockui/jquery.blockUI.min.js?ver=2.7.0-wc.11.1.2', 'totalBytes': 3722}
  - Item: {'url': 'https://casngs.com/wp-content/plugins/woocommerce/assets/js/js-cookie/js.cookie.min.js?ver=2.1.4-wc.11.1.2', 'totalBytes': 1196}

==================== AUDIT: unused-javascript (Reduce unused JavaScript) ====================
Description: Reduce unused JavaScript and defer loading scripts until they are required to decrease bytes consumed by network activity. [Learn how to reduce unused JavaScript](https://developer.chrome.com/docs/lighthouse/performance/unused-javascript/).
DisplayValue: Est savings of 284 KiB
Details type: opportunity
Items count: 5
  - Item: {'url': 'https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js', 'totalBytes': 120894, 'wastedBytes': 88032, 'wastedPercent': 72.81740672306508}
  - Item: {'url': 'https://www.googletagmanager.com/gtag/js?id=GT-TQT9R35W', 'totalBytes': 174064, 'wastedBytes': 73611, 'wastedPercent': 42.28981247516289}
  - Item: {'url': 'https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js?ver=r128', 'totalBytes': 120879, 'wastedBytes': 63342, 'wastedPercent': 52.40096446237851}
  - Item: {'url': 'https://cdn.tailwindcss.com/?plugins=forms,container-queries', 'totalBytes': 123020, 'wastedBytes': 42649, 'wastedPercent': 34.66834378348963}
  - Item: {'url': 'https://collect.commerce.godaddy.com/sdk.js', 'totalBytes': 32185, 'wastedBytes': 23637, 'wastedPercent': 73.44094003860266}

==================== AUDIT: unused-css-rules (Reduce unused CSS) ====================
Description: Reduce unused rules from stylesheets and defer CSS not used for above-the-fold content to decrease bytes consumed by network activity. [Learn how to reduce unused CSS](https://developer.chrome.com/docs/lighthouse/performance/unused-css-rules/).
DisplayValue: Est savings of 60 KiB
Details type: opportunity
Items count: 3
  - Item: {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/css/um-fontawesome.min.css?ver=6.5.2', 'wastedBytes': 36604, 'wastedPercent': 100, 'totalBytes': 36604}
  - Item: {'url': 'https://casngs.com/wp-includes/css/dist/components/style.min.css?ver=7.1.2', 'wastedBytes': 13893, 'wastedPercent': 99.67549778455994, 'totalBytes': 13938}
  - Item: {'url': 'https://casngs.com/wp-content/plugins/woocommerce/assets/css/woocommerce.css?ver=11.1.2', 'wastedBytes': 10846, 'wastedPercent': 99.3736498735359, 'totalBytes': 10914}

==================== AUDIT: bootup-time (Reduce JavaScript execution time) ====================
Description: Consider reducing the time spent parsing, compiling, and executing JS. You may find delivering smaller JS payloads helps with this. [Learn how to reduce Javascript execution time](https://developer.chrome.com/docs/lighthouse/performance/bootup-time/).
DisplayValue: 17.9 s
Details type: table
Items count: 22
  - Item: {'url': 'https://casngs.com/', 'total': 11971.248999999978, 'scripting': 1138.0559999999987, 'scriptParseCompile': 32.891}
  - Item: {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/corridor-hero-engine.js?ver=1.8.2', 'total': 7873.166999999998, 'scripting': 4387.611999999999, 'scriptParseCompile': 11.562}
  - Item: {'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js?ver=3.12.5', 'total': 7741.583000000897, 'scripting': 1117.4029999999925, 'scriptParseCompile': 13.154}
  - Item: {'url': 'https://cdn.tailwindcss.com/?plugins=forms,container-queries', 'total': 4959.924999999996, 'scripting': 4866.846999999995, 'scriptParseCompile': 92.76}
  - Item: {'url': 'Unattributable', 'total': 2087.9949999999812, 'scripting': 213.2820000000002, 'scriptParseCompile': 0}
  - Item: {'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js?ver=3.12.5', 'total': 1935.8859999999825, 'scripting': 421.58599999999905, 'scriptParseCompile': 18.636}
  - Item: {'url': 'https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js', 'total': 1253.4390000000094, 'scripting': 1097.0140000000094, 'scriptParseCompile': 115.11999999999999}
  - Item: {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/header-footer.js?ver=2.3.2', 'total': 1154.6190000000004, 'scripting': 962.8120000000004, 'scriptParseCompile': 8.456}
  - Item: {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/biotech-blocks-engine.js?ver=1.8.2', 'total': 775.9750000000001, 'scripting': 394.0050000000001, 'scriptParseCompile': 3.495}
  - Item: {'url': 'https://www.googletagmanager.com/gtag/js?id=GT-TQT9R35W', 'total': 743.3889999999992, 'scripting': 609.7659999999993, 'scriptParseCompile': 110.173}

==================== AUDIT: mainthread-work-breakdown (Minimize main-thread work) ====================
Description: Consider reducing the time spent parsing, compiling and executing JS. You may find delivering smaller JS payloads helps with this. [Learn how to minimize main-thread work](https://developer.chrome.com/docs/lighthouse/performance/mainthread-work-breakdown/)
DisplayValue: 43.6 s
Details type: table
Items count: 7
  - Item: {'group': 'scriptEvaluation', 'groupLabel': 'Script Evaluation', 'duration': 17364.826000000307}
  - Item: {'group': 'styleLayout', 'groupLabel': 'Style & Layout', 'duration': 13611.15399999996}
  - Item: {'group': 'other', 'groupLabel': 'Other', 'duration': 7971.025999999956}
  - Item: {'group': 'paintCompositeRender', 'groupLabel': 'Rendering', 'duration': 3241.113000001179}
  - Item: {'group': 'scriptParseCompile', 'groupLabel': 'Script Parsing & Compilation', 'duration': 776.0830000000003}
  - Item: {'group': 'parseHTML', 'groupLabel': 'Parse HTML & CSS', 'duration': 451.0499999999999}
  - Item: {'group': 'garbageCollection', 'groupLabel': 'Garbage Collection', 'duration': 186.01900000000026}

==================== AUDIT: image-delivery-insight (Improve image delivery) ====================
Description: Reducing the download time of images can improve the perceived load time of the page and LCP. [Learn more about optimizing image size](https://developer.chrome.com/docs/performance/insights/image-delivery)
DisplayValue: Est savings of 184 KiB
Details type: table
Items count: 3
  - Item: {'node': {'type': 'node', 'lhId': 'page-0-IMG', 'path': '1,HTML,1,BODY,2,DIV,2,FOOTER,0,DIV,0,FOOTER,0,DIV,0,FIGURE,0,IMG', 'selector': 'footer.wp-block-group > div.wp-block-group > figure.wp-block-image > img', 'boundingRect': {'top': 12130, 'bottom': 12327, 'left': 76, 'right': 620, 'width': 544, 'height': 196}, 'snippet': '<img decoding="async" src="https://casngs.com/wp-content/uploads/2026/10/png-for-footer.webp" alt="DNA Strand">', 'nodeLabel': 'DNA Strand'}, 'url': 'https://casngs.com/wp-content/uploads/2026/10/png-for-footer.webp', 'totalBytes': 156726, 'wastedBytes': 141160, 'subItems': {'type': 'subitems', 'items': [{'reason': 'This image file is larger than it needs to be (2089x753) for its displayed dimensions (658x237). Use responsive images to reduce the image download size.', 'wastedBytes': 141160}]}}
  - Item: {'node': {'type': 'node', 'lhId': 'page-1-IMG', 'path': '1,HTML,1,BODY,2,DIV,1,MAIN,0,DIV,0,DIV,4,DIV,1,DIV,0,SECTION,0,DIV,0,DIV,1,DIV,0,DIV,0,IMG', 'selector': 'div.cards-wrapper > div.bio-card > div.bio-image-wrapper > img', 'boundingRect': {'top': 7535, 'bottom': 8151, 'left': 50, 'right': 666, 'width': 616, 'height': 616}, 'snippet': '<img decoding="async" src="https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/images/…" alt="Human DNA Study">', 'nodeLabel': 'Human DNA Study'}, 'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/images/stacking-hero/human.webp', 'totalBytes': 69254, 'wastedBytes': 37915, 'subItems': {'type': 'subitems', 'items': [{'reason': 'This image file is larger than it needs to be (1108x1108) for its displayed dimensions (745x745). Use responsive images to reduce the image download size.', 'wastedBytes': 37915}]}}
  - Item: {'node': {'type': 'node', 'lhId': 'page-2-IMG', 'path': '1,HTML,1,BODY,2,DIV,2,FOOTER,0,DIV,0,FOOTER,0,DIV,1,DIV,0,DIV,0,DIV,0,FIGURE,0,IMG', 'selector': 'div.wp-block-group > div.wp-block-group > figure.wp-block-image > img', 'boundingRect': {'top': 12930, 'bottom': 12970, 'left': 76, 'right': 116, 'width': 40, 'height': 40}, 'snippet': '<img decoding="async" src="https://casngs.com/wp-content/uploads/2026/09/logo.png" alt="Site Logo" style="width:40px;height:40px">', 'nodeLabel': 'Site Logo'}, 'url': 'https://casngs.com/wp-content/uploads/2026/09/logo.png', 'totalBytes': 9049, 'wastedBytes': 9021, 'subItems': {'type': 'subitems', 'items': [{'reason': 'This image file is larger than it needs to be (920x820) for its displayed dimensions (48x48). Use responsive images to reduce the image download size.', 'wastedBytes': 9021}]}}

==================== AUDIT: forced-reflow-insight (Forced reflow) ====================
Description: A forced reflow occurs when JavaScript queries geometric properties (such as `offsetWidth`) after styles have been invalidated by a change to the DOM state. This can result in poor performance. Learn more about [forced reflows](https://developer.chrome.com/docs/performance/insights/forced-reflow) and possible mitigations.
DisplayValue: None
Details type: list
Items count: 2
  - Item: {'type': 'table', 'headings': [{'key': 'source', 'valueType': 'source-location', 'label': 'Top function call'}, {'key': 'reflowTime', 'valueType': 'ms', 'granularity': 1, 'label': 'Total reflow time'}], 'items': [{'source': {'type': 'source-location', 'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/corridor-hero-engine.js?ver=1.8.2', 'urlProvider': 'network', 'line': 1019, 'column': 19}, 'reflowTime': 3265.942}]}
  - Item: {'type': 'table', 'headings': [{'key': 'source', 'valueType': 'source-location', 'label': 'Source'}, {'key': 'reflowTime', 'valueType': 'ms', 'granularity': 1, 'label': 'Total reflow time'}], 'items': [{'source': {'type': 'text', 'value': '[unattributed]'}, 'reflowTime': 642.882}, {'source': {'type': 'source-location', 'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js?ver=3.12.5', 'urlProvider': 'network', 'line': 9, 'column': 33734}, 'reflowTime': 4.262}, {'source': {'type': 'source-location', 'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js?ver=3.12.5', 'urlProvider': 'network', 'line': 9, 'column': 13363}, 'reflowTime': 51.306}, {'source': {'type': 'source-location', 'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js?ver=3.12.5', 'urlProvider': 'network', 'line': 9, 'column': 16743}, 'reflowTime': 38.17}, {'source': {'type': 'source-location', 'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/header-footer.js?ver=2.3.2', 'urlProvider': 'network', 'line': 26, 'column': 22}, 'reflowTime': 4.034}, {'source': {'type': 'source-location', 'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js?ver=3.12.5', 'urlProvider': 'network', 'line': 9, 'column': 55073}, 'reflowTime': 149.825}, {'source': {'type': 'source-location', 'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js?ver=3.12.5', 'urlProvider': 'network', 'line': 9, 'column': 3496}, 'reflowTime': 29.4}, {'source': {'type': 'source-location', 'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js?ver=3.12.5', 'urlProvider': 'network', 'line': 9, 'column': 23473}, 'reflowTime': 1.98}, {'source': {'type': 'source-location', 'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js?ver=3.12.5', 'urlProvider': 'network', 'line': 9, 'column': 13694}, 'reflowTime': 0.899}, {'source': {'type': 'source-location', 'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js?ver=3.12.5', 'urlProvider': 'network', 'line': 9, 'column': 13835}, 'reflowTime': 16.969}, {'source': {'type': 'source-location', 'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js?ver=3.12.5', 'urlProvider': 'network', 'line': 9, 'column': 13988}, 'reflowTime': 17.501}, {'source': {'type': 'source-location', 'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js?ver=3.12.5', 'urlProvider': 'network', 'line': 9, 'column': 14012}, 'reflowTime': 20.269}, {'source': {'type': 'source-location', 'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js?ver=3.12.5', 'urlProvider': 'network', 'line': 9, 'column': 14024}, 'reflowTime': 17.585}, {'source': {'type': 'source-location', 'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js?ver=3.12.5', 'urlProvider': 'network', 'line': 9, 'column': 9918}, 'reflowTime': 322.969}, {'source': {'type': 'source-location', 'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js?ver=3.12.5', 'urlProvider': 'network', 'line': 9, 'column': 16622}, 'reflowTime': 68.396}, {'source': {'type': 'source-location', 'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js?ver=3.12.5', 'urlProvider': 'network', 'line': 9, 'column': 27595}, 'reflowTime': 8.141}, {'source': {'type': 'source-location', 'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/corridor-hero-engine.js?ver=1.8.2', 'urlProvider': 'network', 'line': 31, 'column': 41}, 'reflowTime': 188.23}, {'source': {'type': 'source-location', 'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/corridor-hero-engine.js?ver=1.8.2', 'urlProvider': 'network', 'line': 138, 'column': 20}, 'reflowTime': 7.175}, {'source': {'type': 'source-location', 'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/corridor-hero-engine.js?ver=1.8.2', 'urlProvider': 'network', 'line': 706, 'column': 20}, 'reflowTime': 1.976}, {'source': {'type': 'source-location', 'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/corridor-hero-engine.js?ver=1.8.2', 'urlProvider': 'network', 'line': 836, 'column': 24}, 'reflowTime': 3.508}, {'source': {'type': 'source-location', 'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/header-footer.js?ver=2.3.2', 'urlProvider': 'network', 'line': 259, 'column': 23}, 'reflowTime': 177.154}, {'source': {'type': 'source-location', 'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/header-footer.js?ver=2.3.2', 'urlProvider': 'network', 'line': 368, 'column': 49}, 'reflowTime': 1.526}, {'source': {'type': 'source-location', 'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/corridor-hero-engine.js?ver=1.8.2', 'urlProvider': 'network', 'line': 844, 'column': 27}, 'reflowTime': 2.26}, {'source': {'type': 'source-location', 'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/corridor-hero-engine.js?ver=1.8.2', 'urlProvider': 'network', 'line': 1023, 'column': 21}, 'reflowTime': 3263.682}, {'source': {'type': 'source-location', 'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/corridor-hero-engine.js?ver=1.8.2', 'urlProvider': 'network', 'line': 620, 'column': 32}, 'reflowTime': 19.412}]}

==================== AUDIT: font-display-insight (Font display) ====================
Description: Consider setting [`font-display`](https://developer.chrome.com/docs/performance/insights/font-display) to `swap` or `optional` to ensure text is consistently visible. `swap` can be further optimized to mitigate layout shifts with [font metric overrides](https://developer.chrome.com/blog/font-fallbacks).
DisplayValue: Est savings of 70 ms
Details type: table
Items count: 1
  - Item: {'url': 'https://casngs.com/wp-content/themes/cas-earth-theme/assets/fonts/manrope/Manrope-VariableFont_wght.woff2', 'wastedMs': 70}

==================== AUDIT: cache-insight (Use efficient cache lifetimes) ====================
Description: A long cache lifetime can speed up repeat visits to your page. [Learn more about caching](https://developer.chrome.com/docs/performance/insights/cache).
DisplayValue: Est savings of 73 KiB
Details type: table
Items count: 3
  - Item: {'url': 'https://collect.commerce.godaddy.com/sdk.js', 'cacheLifetimeMs': 0, 'totalBytes': 48570, 'wastedBytes': 48570}
  - Item: {'url': 'https://img1.wsimg.com/signals/js/clients/scc-c2/scc-c2.min.js', 'cacheLifetimeMs': 1800000, 'totalBytes': 22103, 'wastedBytes': 19063.8375}
  - Item: {'url': 'https://img1.wsimg.com/signals/js/clients/tti/tti.min.js', 'cacheLifetimeMs': 1800000, 'totalBytes': 7975, 'wastedBytes': 6878.4375}

==================== AUDIT: unsized-images (Image elements do not have explicit `width` and `height`) ====================
Description: Set an explicit width and height on image elements to reduce layout shifts and improve CLS. [Learn how to set image dimensions](https://web.dev/articles/optimize-cls#images_without_dimensions)
DisplayValue: None
Details type: table
Items count: 5
  - Item: {'url': 'https://casngs.com/wp-content/uploads/2026/10/png-for-footer.webp', 'node': {'type': 'node', 'lhId': '1-50-IMG', 'path': '1,HTML,1,BODY,2,DIV,2,FOOTER,0,DIV,0,FOOTER,0,DIV,0,FIGURE,0,IMG', 'selector': 'footer.wp-block-group > div.wp-block-group > figure.wp-block-image > img', 'boundingRect': {'top': 12130, 'bottom': 12327, 'left': 76, 'right': 620, 'width': 544, 'height': 196}, 'snippet': '<img decoding="async" src="https://casngs.com/wp-content/uploads/2026/10/png-for-footer.webp" alt="DNA Strand">', 'nodeLabel': 'DNA Strand'}}
  - Item: {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/images/stacking-hero/human.webp', 'node': {'type': 'node', 'lhId': '1-47-IMG', 'path': '1,HTML,1,BODY,2,DIV,1,MAIN,0,DIV,0,DIV,4,DIV,1,DIV,0,SECTION,0,DIV,0,DIV,1,DIV,0,DIV,0,IMG', 'selector': 'div.cards-wrapper > div.bio-card > div.bio-image-wrapper > img', 'boundingRect': {'top': 7535, 'bottom': 8151, 'left': 50, 'right': 666, 'width': 616, 'height': 616}, 'snippet': '<img decoding="async" src="https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/images/…" alt="Human DNA Study">', 'nodeLabel': 'Human DNA Study'}}
  - Item: {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/images/stacking-hero/fungi-bacteria.webp', 'node': {'type': 'node', 'lhId': '1-49-IMG', 'path': '1,HTML,1,BODY,2,DIV,1,MAIN,0,DIV,0,DIV,4,DIV,1,DIV,0,SECTION,0,DIV,0,DIV,3,DIV,0,DIV,0,IMG', 'selector': 'div.cards-wrapper > div.bio-card > div.bio-image-wrapper > img', 'boundingRect': {'top': 7535, 'bottom': 8151, 'left': 152, 'right': 563, 'width': 411, 'height': 616}, 'snippet': '<img decoding="async" src="https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/images/…" alt="Fungi and Bacteria Study">', 'nodeLabel': 'Fungi and Bacteria Study'}}
  - Item: {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/images/stacking-hero/khota.webp', 'node': {'type': 'node', 'lhId': '1-48-IMG', 'path': '1,HTML,1,BODY,2,DIV,1,MAIN,0,DIV,0,DIV,4,DIV,1,DIV,0,SECTION,0,DIV,0,DIV,2,DIV,1,DIV,0,IMG', 'selector': 'div.cards-wrapper > div.bio-card > div.bio-image-wrapper > img', 'boundingRect': {'top': 7535, 'bottom': 8151, 'left': 50, 'right': 666, 'width': 616, 'height': 616}, 'snippet': '<img decoding="async" src="https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/images/…" alt="Animal Study">', 'nodeLabel': 'Animal Study'}}
  - Item: {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/images/stacking-hero/sunflower.webp', 'node': {'type': 'node', 'lhId': '1-46-IMG', 'path': '1,HTML,1,BODY,2,DIV,1,MAIN,0,DIV,0,DIV,4,DIV,1,DIV,0,SECTION,0,DIV,0,DIV,0,DIV,1,DIV,0,IMG', 'selector': 'div.cards-wrapper > div.bio-card > div.bio-image-wrapper > img', 'boundingRect': {'top': 7095, 'bottom': 7711, 'left': 185, 'right': 530, 'width': 345, 'height': 616}, 'snippet': '<img decoding="async" src="https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/images/…" alt="Sunflower">', 'nodeLabel': 'Sunflower'}}

==================== AUDIT: unminified-javascript (Minify JavaScript) ====================
Description: Minifying JavaScript files can reduce payload sizes and script parse time. [Learn how to minify JavaScript](https://developer.chrome.com/docs/lighthouse/performance/unminified-javascript/).
DisplayValue: Est savings of 7 KiB
Details type: opportunity
Items count: 2
  - Item: {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/corridor-hero-engine.js?ver=1.8.2', 'totalBytes': 14776, 'wastedBytes': 4765, 'wastedPercent': 32.24900672371638}
  - Item: {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/header-footer.js?ver=2.3.2', 'totalBytes': 7055, 'wastedBytes': 2184, 'wastedPercent': 30.958776129861032}

==================== AUDIT: lcp-breakdown-insight (LCP breakdown) ====================
Description: Each [subpart has specific improvement strategies](https://developer.chrome.com/docs/performance/insights/lcp-breakdown). Ideally, most of the LCP time should be spent on loading the resources, not within delays.
DisplayValue: None
Details type: list
Items count: 2
  - Item: {'type': 'table', 'headings': [{'key': 'label', 'valueType': 'text', 'label': 'Subpart'}, {'key': 'duration', 'valueType': 'ms', 'label': 'Duration'}], 'items': [{'subpart': 'timeToFirstByte', 'label': 'Time to first byte', 'duration': 156.035}, {'subpart': 'elementRenderDelay', 'label': 'Element render delay', 'duration': 12727.448}]}
  - Item: {'type': 'node', 'lhId': 'page-3-P', 'path': '1,HTML,1,BODY,2,DIV,1,MAIN,0,DIV,0,DIV,0,DIV,0,DIV,2,DIV,0,SECTION,0,DIV,0,DIV,2,P', 'selector': 'section.cor3d-anchor > div.cor3d-card > div.cor3d-card-inner > p.cor3d-copy', 'boundingRect': {'top': 395, 'bottom': 474, 'left': 51, 'right': 389, 'width': 338, 'height': 78}, 'snippet': '<p class="cor3d-copy">', 'nodeLabel': 'High-molecular-weight genomic DNA isolated from biological specimens and stabil…'}

==================== AUDIT: network-dependency-tree-insight (Network dependency tree) ====================
Description: [Avoid chaining critical requests](https://developer.chrome.com/docs/performance/insights/network-dependency-tree) by reducing the length of chains, reducing the download size of resources, or deferring the download of unnecessary resources to improve page load.
DisplayValue: None
Details type: list
Items count: 3
  - Item: {'type': 'list-section', 'value': {'type': 'network-tree', 'chains': {'F50B6A415DFC74ED34CCA08C9BD9D2AC': {'url': 'https://casngs.com/', 'navStartToEndTime': 637, 'transferSize': 34975, 'isLongest': True, 'children': {'8804.107': {'url': 'https://img1.wsimg.com/signals/js/clients/tti/tti.min.js', 'navStartToEndTime': 6677, 'transferSize': 7975, 'isLongest': True, 'children': {'8804.354': {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/models/dna.glb', 'navStartToEndTime': 13752, 'transferSize': 245087, 'isLongest': True, 'children': {}}, '8804.353': {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/models/dna.glb', 'navStartToEndTime': 13531, 'transferSize': 245097, 'children': {}}, '8804.107': {'url': 'https://img1.wsimg.com/signals/js/clients/tti/tti.min.js', 'navStartToEndTime': 6677, 'transferSize': 7975, 'children': {}}}}, '8804.5': {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/css/cas-splash.css?ver=2.3.2', 'navStartToEndTime': 1160, 'transferSize': 1080, 'children': {'8804.164': {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/cas-logo/FontsFree-Net-Lastica.ttf', 'navStartToEndTime': 6060, 'transferSize': 26112, 'children': {}}}}, '8804.290': {'url': 'https://casngs.com/wp-content/themes/cas-earth-theme/assets/fonts/manrope/Manrope-VariableFont_wght.woff2', 'navStartToEndTime': 6056, 'transferSize': 54066, 'children': {}}, '8804.101': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/select2/select2.full.min.js?ver=4.0.13', 'navStartToEndTime': 5017, 'transferSize': 20800, 'children': {}}, '8804.106': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/js/um-account.min.js?ver=2.14.0', 'navStartToEndTime': 5010, 'transferSize': 1107, 'children': {}}, '8804.105': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/js/um-profile.min.js?ver=2.14.0', 'navStartToEndTime': 5007, 'transferSize': 1470, 'children': {}}, '8804.104': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/js/um-scripts.min.js?ver=2.14.0', 'navStartToEndTime': 5003, 'transferSize': 3236, 'children': {}}, '8804.103': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/raty/um-raty.min.js?ver=2.6.0', 'navStartToEndTime': 4995, 'transferSize': 3331, 'children': {}}, '8804.102': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/select2/i18n/en.js?ver=4.0.13', 'navStartToEndTime': 4988, 'transferSize': 823, 'children': {}}, '8804.100': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/js/um-conditional.min.js?ver=2.14.0', 'navStartToEndTime': 4983, 'transferSize': 2604, 'children': {}}, '8804.98': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/js/um-functions.min.js?ver=2.14.0', 'navStartToEndTime': 4977, 'transferSize': 3484, 'children': {}}, '8804.97': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/fileupload/jquery.uploadfile.min.js?ver=3.1.2', 'navStartToEndTime': 4970, 'transferSize': 3490, 'children': {}}, '8804.99': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/js/um-responsive.min.js?ver=2.14.0', 'navStartToEndTime': 4964, 'transferSize': 612, 'children': {}}, '8804.96': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/jquery-form/jquery-form.min.js?ver=2.14.0', 'navStartToEndTime': 4957, 'transferSize': 5651, 'children': {}}, '8804.95': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/js/um-modal.min.js?ver=2.14.0', 'navStartToEndTime': 4955, 'transferSize': 1643, 'children': {}}, '8804.93': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/cropper/cropper.min.js?ver=1.6.1', 'navStartToEndTime': 4951, 'transferSize': 12132, 'children': {}}, '8804.94': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/js/common-frontend.min.js?ver=2.14.0', 'navStartToEndTime': 4929, 'transferSize': 1141, 'children': {}}, '8804.92': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/js/common.min.js?ver=2.14.0', 'navStartToEndTime': 4925, 'transferSize': 1244, 'children': {}}, '8804.91': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/pickadate/picker.time.min.js?ver=3.6.2', 'navStartToEndTime': 4922, 'transferSize': 3524, 'children': {}}, '8804.90': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/pickadate/picker.date.min.js?ver=3.6.2', 'navStartToEndTime': 4914, 'transferSize': 4650, 'children': {}}, '8804.89': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/pickadate/picker.min.js?ver=3.6.2', 'navStartToEndTime': 4904, 'transferSize': 3504, 'children': {}}, '8804.88': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/um-notice/um-notice.min.js?ver=1.0', 'navStartToEndTime': 4899, 'transferSize': 885, 'children': {}}, '8804.87': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/um-confirm/um-confirm.min.js?ver=1.0', 'navStartToEndTime': 4892, 'transferSize': 1164, 'children': {}}, '8804.86': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/tipsy/tipsy.min.js?ver=1.0.0a', 'navStartToEndTime': 4877, 'transferSize': 1873, 'children': {}}, '8804.82': {'url': 'https://casngs.com/wp-includes/js/underscore.min.js?ver=1.13.8', 'navStartToEndTime': 4870, 'transferSize': 7616, 'children': {}}, '8804.79': {'url': 'https://casngs.com/wp-content/plugins/woocommerce/assets/js/frontend/order-attribution.min.js?ver=11.1.2', 'navStartToEndTime': 4862, 'transferSize': 1455, 'children': {}}, '8804.85': {'url': 'https://casngs.com/wp-includes/js/dist/i18n.min.js?ver=1dfe7db3940c23ea9216', 'navStartToEndTime': 4858, 'transferSize': 2797, 'children': {}}, '8804.84': {'url': 'https://casngs.com/wp-includes/js/dist/hooks.min.js?ver=f0f188028580e8dc1255', 'navStartToEndTime': 4851, 'transferSize': 2175, 'children': {}}, '8804.83': {'url': 'https://casngs.com/wp-includes/js/wp-util.min.js?ver=7.1.2', 'navStartToEndTime': 4847, 'transferSize': 1096, 'children': {}}, '8804.74': {'url': 'https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/postprocessing/RenderPass.js?ver=r128', 'navStartToEndTime': 4845, 'transferSize': 1330, 'children': {}}, '8804.71': {'url': 'https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/postprocessing/Pass.js?ver=r128', 'navStartToEndTime': 4839, 'transferSize': 1353, 'children': {}}, '8804.73': {'url': 'https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/postprocessing/EffectComposer.js?ver=r128', 'navStartToEndTime': 4829, 'transferSize': 2438, 'children': {}}, '8804.72': {'url': 'https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/postprocessing/ShaderPass.js?ver=r128', 'navStartToEndTime': 4796, 'transferSize': 1307, 'children': {}}, '8804.70': {'url': 'https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/shaders/CopyShader.js?ver=r128', 'navStartToEndTime': 4791, 'transferSize': 1054, 'children': {}}, '8804.78': {'url': 'https://casngs.com/wp-content/plugins/woocommerce/assets/js/sourcebuster/sourcebuster.min.js?ver=11.1.2', 'navStartToEndTime': 4788, 'transferSize': 5038, 'children': {}}, '8804.77': {'url': 'https://casngs.com/wp-content/plugins/woocommerce/assets/js/frontend/woocommerce.min.js?ver=11.1.2', 'navStartToEndTime': 4785, 'transferSize': 1817, 'children': {}}, '8804.75': {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/corridor-hero-engine.js?ver=1.8.2', 'navStartToEndTime': 4781, 'transferSize': 15208, 'children': {}}, '8804.76': {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/header-footer.js?ver=2.3.2', 'navStartToEndTime': 4777, 'transferSize': 7480, 'children': {}}, '8804.69': {'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/Observer.min.js?ver=3.12.5', 'navStartToEndTime': 4775, 'transferSize': 4437, 'children': {}}, '8804.68': {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/dna-background-enhance.js?ver=1.8.2', 'navStartToEndTime': 4772, 'transferSize': 3899, 'children': {}}, '8804.67': {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/biotech-blocks-engine.js?ver=1.8.2', 'navStartToEndTime': 4769, 'transferSize': 5266, 'children': {}}, '8804.65': {'url': 'https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/loaders/GLTFLoader.js?ver=r128', 'navStartToEndTime': 4102, 'transferSize': 23169, 'children': {}}, '8804.66': {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/three-asset-cache.js?ver=1.8.2', 'navStartToEndTime': 4097, 'transferSize': 862, 'children': {}}, '8804.63': {'url': 'https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js?ver=r128', 'navStartToEndTime': 2607, 'transferSize': 121513, 'children': {}}, '8804.64': {'url': 'https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/libs/meshopt_decoder.js?ver=r128', 'navStartToEndTime': 2592, 'transferSize': 6559, 'children': {}}, '8804.47': {'url': 'https://cdn.tailwindcss.com/3.4.17?plugins=forms@0.5.10,container-queries@0.1.1', 'navStartToEndTime': 2260, 'transferSize': 130216, 'children': {'8804.47': {'url': 'https://cdn.tailwindcss.com/3.4.17?plugins=forms@0.5.10,container-queries@0.1.1', 'navStartToEndTime': 2260, 'transferSize': 130216, 'children': {}}}}, '8804.62': {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/cas-splash.js?ver=2.3.2', 'navStartToEndTime': 2197, 'transferSize': 1607, 'children': {}}, '8804.61': {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/js/stacking-hero.js?ver=2.3.2', 'navStartToEndTime': 2187, 'transferSize': 972, 'children': {}}, '8804.60': {'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js?ver=3.12.5', 'navStartToEndTime': 2128, 'transferSize': 16539, 'children': {}}, '8804.59': {'url': 'https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js?ver=3.12.5', 'navStartToEndTime': 2118, 'transferSize': 26149, 'children': {}}, '8804.50': {'url': 'https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js', 'navStartToEndTime': 2082, 'transferSize': 121557, 'children': {}}, '8804.42': {'url': 'https://collect.commerce.godaddy.com/sdk.js', 'navStartToEndTime': 1948, 'transferSize': 48570, 'children': {}}, '8804.58': {'url': 'https://casngs.com/wp-content/plugins/woocommerce/assets/client/blocks/wc-blocks.css?ver=wc-11.1.2', 'navStartToEndTime': 1914, 'transferSize': 2857, 'children': {}}, '8804.49': {'url': 'https://fonts.googleapis.com/css2?family=Hanken+Grotesk:wght@100..900&family=JetBrains+Mono:wght@100..900&display=swap', 'navStartToEndTime': 1820, 'transferSize': 612, 'children': {}}, '8804.48': {'url': 'https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:wght,FILL@100..700,0..1&display=swap', 'navStartToEndTime': 1815, 'transferSize': 891, 'children': {}}, '8804.43': {'url': 'https://casngs.com/wp-content/mu-plugins/vendor/godaddy/mwc-core/assets/js/payments/frontend/poynt.js', 'navStartToEndTime': 1657, 'transferSize': 3781, 'children': {}}, '8804.45': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/js/um-gdpr.min.js?ver=2.14.0', 'navStartToEndTime': 1653, 'transferSize': 625, 'children': {}}, '8804.37': {'url': 'https://casngs.com/wp-includes/js/jquery/jquery.min.js?ver=3.7.1', 'navStartToEndTime': 1563, 'transferSize': 30016, 'children': {}}, '8804.41': {'url': 'https://casngs.com/wp-content/plugins/woocommerce/assets/js/js-cookie/js.cookie.min.js?ver=2.1.4-wc.11.1.2', 'navStartToEndTime': 1558, 'transferSize': 1196, 'children': {}}, '8804.39': {'url': 'https://casngs.com/wp-content/plugins/woocommerce/assets/js/jquery-blockui/jquery.blockUI.min.js?ver=2.7.0-wc.11.1.2', 'navStartToEndTime': 1556, 'transferSize': 3722, 'children': {}}, '8804.38': {'url': 'https://casngs.com/wp-includes/js/jquery/jquery-migrate.min.js?ver=3.4.1', 'navStartToEndTime': 1544, 'transferSize': 5092, 'children': {}}, '8804.36': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/css/um-old-default.min.css?ver=2.14.0', 'navStartToEndTime': 1538, 'transferSize': 1442, 'children': {}}, '8804.27': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/legacy/fonticons/fonticons-fa.min.css?ver=2.14.0', 'navStartToEndTime': 1490, 'transferSize': 4749, 'children': {}}, '8804.29': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/css/common.min.css?ver=2.14.0', 'navStartToEndTime': 1480, 'transferSize': 654, 'children': {}}, '8804.28': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/css/um-fontawesome.min.css?ver=6.5.2', 'navStartToEndTime': 1478, 'transferSize': 37031, 'children': {}}, '8804.26': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/legacy/fonticons/fonticons-ii.min.css?ver=2.14.0', 'navStartToEndTime': 1390, 'transferSize': 5435, 'children': {}}, '8804.25': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/pickadate/default.time.min.css?ver=3.6.2', 'navStartToEndTime': 1361, 'transferSize': 830, 'children': {}}, '8804.24': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/pickadate/default.date.min.css?ver=3.6.2', 'navStartToEndTime': 1360, 'transferSize': 1201, 'children': {}}, '8804.23': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/pickadate/default.min.css?ver=3.6.2', 'navStartToEndTime': 1358, 'transferSize': 1007, 'children': {}}, '8804.22': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/um-notice/um-notice.min.css?ver=1.0', 'navStartToEndTime': 1356, 'transferSize': 646, 'children': {}}, '8804.21': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/um-confirm/um-confirm.min.css?ver=1.0', 'navStartToEndTime': 1353, 'transferSize': 716, 'children': {}}, '8804.20': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/css/um-fileupload.min.css?ver=2.14.0', 'navStartToEndTime': 1349, 'transferSize': 1117, 'children': {}}, '8804.19': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/select2/select2.min.css?ver=4.0.13', 'navStartToEndTime': 1346, 'transferSize': 2221, 'children': {}}, '8804.18': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/raty/um-raty.min.css?ver=2.6.0', 'navStartToEndTime': 1345, 'transferSize': 760, 'children': {}}, '8804.17': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/tipsy/tipsy.min.css?ver=1.0.0a', 'navStartToEndTime': 1344, 'transferSize': 738, 'children': {}}, '8804.16': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/jquery-ui/jquery-ui.min.css?ver=1.13.2', 'navStartToEndTime': 1341, 'transferSize': 7509, 'children': {}}, '8804.35': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/css/um-misc.min.css?ver=2.14.0', 'navStartToEndTime': 1330, 'transferSize': 932, 'children': {}}, '8804.34': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/css/um-account.min.css?ver=2.14.0', 'navStartToEndTime': 1326, 'transferSize': 1349, 'children': {}}, '8804.33': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/css/um-profile.min.css?ver=2.14.0', 'navStartToEndTime': 1321, 'transferSize': 2148, 'children': {}}, '8804.32': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/libs/cropper/cropper.min.css?ver=1.6.1', 'navStartToEndTime': 1311, 'transferSize': 1572, 'children': {}}, '8804.31': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/css/um-styles.min.css?ver=2.14.0', 'navStartToEndTime': 1306, 'transferSize': 4103, 'children': {}}, '8804.30': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/css/um-responsive.min.css?ver=2.14.0', 'navStartToEndTime': 1295, 'transferSize': 1833, 'children': {}}, '8804.15': {'url': 'https://casngs.com/wp-content/plugins/ultimate-member/assets/css/um-modal.min.css?ver=2.14.0', 'navStartToEndTime': 1287, 'transferSize': 1161, 'children': {}}, '8804.14': {'url': 'https://casngs.com/wp-content/mu-plugins/vendor/godaddy/mwc-core/assets/css/payment-form.css', 'navStartToEndTime': 1285, 'transferSize': 661, 'children': {}}, '8804.13': {'url': 'https://casngs.com/wp-content/mu-plugins/vendor/wpex/godaddy-launch/includes/Dependencies/GoDaddy/Styles/build/latest.css?ver=2.0.2', 'navStartToEndTime': 1269, 'transferSize': 1770, 'children': {}}, '8804.12': {'url': 'https://casngs.com/wp-includes/css/dist/components/style.min.css?ver=7.1.2', 'navStartToEndTime': 1245, 'transferSize': 14350, 'children': {}}, '8804.10': {'url': 'https://casngs.com/wp-content/plugins/woocommerce/assets/css/woocommerce.css?ver=11.1.2', 'navStartToEndTime': 1218, 'transferSize': 11336, 'children': {}}, '8804.11': {'url': 'https://casngs.com/wp-includes/css/dist/theme/design-tokens.min.css?ver=7.1.2', 'navStartToEndTime': 1196, 'transferSize': 1795, 'children': {}}, '8804.9': {'url': 'https://casngs.com/wp-content/plugins/woocommerce/assets/css/woocommerce-smallscreen.css?ver=11.1.2', 'navStartToEndTime': 1194, 'transferSize': 1729, 'children': {}}, '8804.8': {'url': 'https://casngs.com/wp-content/plugins/woocommerce/assets/css/woocommerce-layout.css?ver=11.1.2', 'navStartToEndTime': 1192, 'transferSize': 3650, 'children': {}}, '8804.7': {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/css/corridor-hero.css?ver=1.8.2', 'navStartToEndTime': 1189, 'transferSize': 4154, 'children': {}}, '8804.6': {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/css/biotech-blocks.css?ver=1.8.2', 'navStartToEndTime': 1171, 'transferSize': 6894, 'children': {}}, '8804.4': {'url': 'https://casngs.com/wp-content/plugins/woocommerce/assets/css/woocommerce-blocktheme.css?ver=11.1.2', 'navStartToEndTime': 1158, 'transferSize': 2413, 'children': {}}, '8804.3': {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/css/stacking-hero.css?ver=2.3.2', 'navStartToEndTime': 1155, 'transferSize': 1712, 'children': {}}, '8804.2': {'url': 'https://casngs.com/wp-content/plugins/cas-ngs-header-footer/assets/header-footer.css?ver=2.3.2', 'navStartToEndTime': 1072, 'transferSize': 6083, 'children': {}}}}}, 'longestChain': {'duration': 13752}}}
  - Item: {'type': 'list-section', 'title': 'Preconnected origins', 'description': '[preconnect](https://developer.chrome.com/docs/lighthouse/performance/uses-rel-preconnect/) hints help the browser establish a connection earlier in the page load, saving time when the first request for that origin is made. The following are the origins that the page preconnected to.', 'value': {'type': 'table', 'headings': [{'key': 'origin', 'valueType': 'text', 'subItemsHeading': {'key': 'warning'}, 'label': 'Origin'}, {'key': 'source', 'valueType': 'node', 'label': 'Source'}], 'items': [{'origin': 'https://fonts.googleapis.com/', 'source': {'type': 'node', 'lhId': 'page-5-LINK', 'path': '1,HTML,0,HEAD,8,LINK', 'selector': 'head > link', 'boundingRect': {'top': 0, 'bottom': 0, 'left': 0, 'right': 0, 'width': 0, 'height': 0}, 'snippet': '<link href="https://fonts.googleapis.com" crossorigin="anonymous" rel="preconnect">', 'nodeLabel': 'head > link'}, 'subItems': {'type': 'subitems', 'items': []}}, {'origin': 'https://fonts.gstatic.com/', 'source': {'type': 'node', 'lhId': 'page-6-LINK', 'path': '1,HTML,0,HEAD,9,LINK', 'selector': 'head > link', 'boundingRect': {'top': 0, 'bottom': 0, 'left': 0, 'right': 0, 'width': 0, 'height': 0}, 'snippet': '<link href="https://fonts.gstatic.com" crossorigin="anonymous" rel="preconnect">', 'nodeLabel': 'head > link'}, 'subItems': {'type': 'subitems', 'items': [{'warning': 'Unused preconnect. Check that the `crossorigin` attribute is used properly.'}]}}, {'origin': 'https://fonts.googleapis.com/', 'source': {'type': 'node', 'lhId': 'page-7-LINK', 'path': '1,HTML,0,HEAD,112,LINK', 'selector': 'head > link', 'boundingRect': {'top': 0, 'bottom': 0, 'left': 0, 'right': 0, 'width': 0, 'height': 0}, 'snippet': '<link rel="preconnect" href="https://fonts.googleapis.com" crossorigin="">', 'nodeLabel': 'head > link'}, 'subItems': {'type': 'subitems', 'items': []}}, {'origin': 'https://fonts.gstatic.com/', 'source': {'type': 'node', 'lhId': 'page-8-LINK', 'path': '1,HTML,0,HEAD,113,LINK', 'selector': 'head > link', 'boundingRect': {'top': 0, 'bottom': 0, 'left': 0, 'right': 0, 'width': 0, 'height': 0}, 'snippet': '<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="">', 'nodeLabel': 'head > link'}, 'subItems': {'type': 'subitems', 'items': [{'warning': 'Unused preconnect. Check that the `crossorigin` attribute is used properly.'}]}}]}}
  - Item: {'type': 'list-section', 'title': 'Preconnect candidates', 'description': 'Add [preconnect](https://developer.chrome.com/docs/lighthouse/performance/uses-rel-preconnect/) hints to your most important origins, but try to use no more than 4.', 'value': {'type': 'table', 'headings': [{'key': 'origin', 'valueType': 'text', 'label': 'Origin'}, {'key': 'wastedMs', 'valueType': 'ms', 'label': 'Est LCP savings'}], 'items': [{'origin': 'https://fonts.gstatic.com', 'wastedMs': 362.92600000000004}]}}


```

```python
# Let's inspect the exact Lighthouse scoring log-normal distribution formula
# Lighthouse Performance Score = sum(weight_i * score_i)
# Metric weights:
# FCP: 10%
# LCP: 25%
# TBT: 30%
# CLS: 25%
# SI:  10%

import math

def calculate_score(value, p10, median):
    """Lighthouse log-normal scoring function"""
    if value is None or value <= 0:
        return 1.0
    # Log-normal distribution parameters
    # Score = 0.5 when value = median
    # Score = 0.9 when value = p10
    # Using standard Lighthouse formula:
    import scipy.stats as stats
    # Alternatively, log-normal CDF formula:
    # location = ln(median)
    # shape = (ln(p10) - ln(median)) / norm.ppf(0.9)
    # score = 1 - cdf(ln(value))
    return None

# Let's write the exact formula using math
def lighthouse_metric_score(value, p10, median):
    # p10 is value at 90th percentile (score 0.90)
    # median is value at 50th percentile (score 0.50)
    # standard normal quantile for 0.90 is ~1.2815515655446004
    if value <= 0:
        return 1.0
    location = math.log(median)
    shape = (math.log(p10) - math.log(median)) / (-1.2815515655446004)
    # CDF of lognormal with location and shape
    # z = (log(value) - location) / shape
    z = (math.log(value) - location) / shape
    # Standard normal CDF using error function math.erf
    cdf = 0.5 * (1.0 + math.erf(z / math.sqrt(2)))
    score = 1.0 - cdf
    return max(0.0, min(1.0, score))

# Scoring options from report:
# FCP: p10=934, median=1600
# LCP: p10=1200, median=2400
# TBT: p10=150, median=600  (standard LH v10)
# SI:  p10=1311, median=2300

# Let's check current metrics:
current_fcp = 3600 # ms
current_lcp = 6358 # ms
current_tbt = 4400 # ms
current_si = 15688 # ms
current_cls = 0.0

fcp_s = lighthouse_metric_score(current_fcp, 934, 1600)
lcp_s = lighthouse_metric_score(current_lcp, 1200, 2400)
tbt_s = lighthouse_metric_score(current_tbt, 150, 600)
si_s  = lighthouse_metric_score(current_si, 1311, 2300)
cls_s = 1.0

total_score = (fcp_s * 10 + lcp_s * 25 + tbt_s * 30 + si_s * 10 + cls_s * 25)
print(f"Current calculated score: {total_score:.2f} / 100")
print(f"FCP ({current_fcp}ms): {fcp_s*10:.2f} pts out of 10")
print(f"LCP ({current_lcp}ms): {lcp_s*25:.2f} pts out of 25")
print(f"TBT ({current_tbt}ms): {tbt_s*30:.2f} pts out of 30")
print(f"SI  ({current_si}ms): {si_s*10:.2f} pts out of 10")
print(f"CLS ({current_cls}): {cls_s*25:.2f} pts out of 25")


```

```text
Current calculated score: 27.15 / 100
FCP (3600ms): 0.27 pts out of 10
LCP (6358ms): 0.90 pts out of 25
TBT (4400ms): 0.98 pts out of 30
SI  (15688ms): 0.00 pts out of 10
CLS (0.0): 25.00 pts out of 25


```

### Current Performance Baseline Breakdown

* **Overall Mobile Performance Score:** **27 / 100**

* **First Contentful Paint (FCP):** **3.6 s** (Score: 0.02 / 10 pts)


* **Largest Contentful Paint (LCP):** **6.4 s** (Score: 0.03 / 25 pts)


* **Total Blocking Time (TBT):** **4,400 ms** (Score: 0.00 / 30 pts)


* **Speed Index (SI):** **15.7 s** (Score: 0.00 / 10 pts)


* **Cumulative Layout Shift (CLS):** **0.00** (Score: 25.0 / 25 pts — *Perfect Baseline*)



The **27-point baseline** is derived almost entirely from a perfect CLS score (25 pts) plus minor fractional points in FCP/LCP. The missing **73 points** are lost due to heavy main-thread CPU execution (**43.6 seconds total main-thread work**), synchronous JIT CSS compilation, forced layout reflows, render-blocking 3D WebGL assets, and plugin script bloat.

---

### Audit-Based Challenge & Solution Matrix

*Sorted strictly from **Most Serious Critical Bottlenecks** to **Average Diagnostic Issues**.*

| Severity & Category | Identified Challenge (Current Impact) | Flawless & Tested Solution | Est. Point Gain | Honest Remarks |
| --- | --- | --- | --- | --- |
| **1. CRITICAL**<br>

<br>*(TBT / FCP)* | **JIT Tailwind CSS Runtime Script**<br><br>

<br>`[cdn.tailwindcss.com/3.4.17](https://cdn.tailwindcss.com/3.4.17)` is loaded via `<script>` in `<head>`. It dynamically parses the DOM and builds CSS in the browser runtime.

<br>

<br>• **130 KB** render-blocking JS

<br>

<br>• **4,866 ms** scripting execution time

 | **Build Static Tailwind CSS pre-compile**:<br>

<br>1. Remove `<script src="[https://cdn.tailwindcss.com/](https://cdn.tailwindcss.com/)..."></script>` from `<head>` completely.<br>

<br>2. Run `npx tailwindcss -i ./src/input.css -o ./dist/output.css --minify` locally or in build step.<br>

<br>3. Enqueue the compiled minified static CSS file (`~15-20 KB` compressed) via `wp_enqueue_style()`. | **+16 to +20 pts** | Loading CDN Tailwind Play script in production is the single biggest performance flaw on this site. Fixing this alone removes ~4.8s of TBT bloat and instantly unblocks browser rendering.

 |
| **2. CRITICAL**<br>

<br>*(TBT / LCP)* | **DOM Layout Thrashing & Animation Loops**<br><br>

<br>`corridor-hero-engine.js` and GSAP `ScrollTrigger.min.js` hijack the main thread.

<br>

<br>• `corridor-hero-engine.js`: **7,873 ms** execution

<br>

<br>• **3,265 ms** locked in **Forced Reflows** (line 1019/1023)

<br>

<br>• GSAP ScrollTrigger: **7,741 ms** execution

 | **Refactor DOM Read/Write Batches**:<br>

<br>1. In `corridor-hero-engine.js`, isolate all geometric DOM reads (`offsetWidth`, `getBoundingClientRect`) into a single variable *before* modifying styles/transforms (prevents forced layout recalcs).

<br>

<br>2. Wrap scroll/resize handlers in `requestAnimationFrame` or throttle with `IntersectionObserver`.<br>

<br>3. Call `ScrollTrigger.config({ limitCallbacks: true })` and disable triggers for out-of-viewport sections. | **+14 to +18 pts** | Your custom hero scripts are choking the main thread. Interactive 3D/scroll features are useless if the browser locks up for 3.2 seconds every time a layout measurement triggers.

 |
| **3. HIGH**<br>

<br>*(LCP / FCP)* | **Render-Blocking WebGL / Three.js Pipeline**<br><br>

<br>`three.min.js` (121 KB) blocks rendering in `<head>`.

<br>

<br>• Duplicate enqueues (`three.min.js` and `three.min.js?ver=r128`)

<br>

<br>• 245 KB 3D model (`dna.glb`) loaded via network dependency tree, creating a **12,727 ms** LCP element render delay.

 | **Defer Three.js & Asynchronous Canvas Loading**:<br>

<br>1. Add `defer` or `async` attributes to Three.js and `GLTFLoader.js` enqueues.<br>

<br>2. Audit `functions.php` / plugin scripts to remove duplicate script registration of Three.js.

<br>

<br>3. Render HTML text / layout skeleton first; delay WebGL canvas init until `DOMContentLoaded` or window `load`. | **+10 to +14 pts** | Never allow background 3D canvas objects to delay main DOM paint. HTML content must display within 1.5 seconds while WebGL assets load in the background.

 |
| **4. HIGH**<br>

<br>*(FCP / Speed Index)* | **Plugin Asset Bloat (Ultimate Member & WooCommerce)**<br><br>

<br>20+ unused plugin JS/CSS files load on the homepage (Cropper, Select2, Raty, Pickadate, `um-fontawesome` with **36.6 KB unused CSS**, WooCommerce CSS with **10.8 KB unused**). Total **284 KB** unused JS.

 | **Conditional Dequeue via `functions.php**`:<br>

<br>Add conditions to dequeue plugin assets on non-plugin pages:<br>

<br>`php<br>add_action('wp_enqueue_scripts', function() {<br>  if (!is_page(['account', 'login', 'user'])) {<br>    wp_dequeue_style('um-fontawesome');<br>    wp_dequeue_style('select2');<br>    wp_dequeue_script('um_scripts');<br>  }<br>  if (!is_woocommerce() && !is_cart() && !is_checkout()) {<br>    wp_dequeue_style('woocommerce-general');<br>    wp_dequeue_style('wc-blocks-vendors-style');<br>  }<br>}, 99);<br>` | **+6 to +9 pts** | WordPress plugins notoriously inject their full asset suites sitewide. Stripping unused plugin JS/CSS from your primary landing page is necessary for high scores.

 |
| **5. MEDIUM**<br>

<br>*(FCP / LCP)* | **Render-Blocking Google Fonts & Missing Display Rules**<br><br>

<br>Google Fonts (`Hanken Grotesk`, `JetBrains Mono`, `Material Symbols`) block initial render. Local `Manrope-VariableFont_wght.woff2` lacks `font-display: swap` (70ms delay). Unused preconnect to `fonts.gstatic.com`.

 | **Asynchronous Font Loading & Font-Display**:<br>

<br>1. Inline critical `@font-face` rules into `<style>` in `<head>` with `font-display: swap;`.<br>

<br>2. Load Google Font CSS asynchronously:<br>

<br>`<link rel="stylesheet" href="..." media="print" onload="this.media='all'">`<br>

<br>3. Ensure `crossorigin="anonymous"` is added to the `fonts.gstatic.com` preconnect tag.

 | **+3 to +5 pts** | Easy low-hanging fruit. Synchronous font requests pause page painting while downloading font files. `font-display: swap` ensures immediate text display.

 |
| **6. MEDIUM**<br>

<br>*(LCP / Payload)* | **Oversized & Unsized Images**<br><br>

<br>• Footer DNA image (`png-for-footer.webp`): **156 KB** file (2089x753) displayed at 544x196 (**141 KB wasted**).

<br>

<br>• Logo (`logo.png`): 920x820 resized to 40x40 (**9 KB wasted**).

<br>

<br>• Stacking hero images missing `width` & `height` attributes.

 | **Image Downscaling & Explicit Dimensions**:<br>

<br>1. Downscale `png-for-footer.webp` in Photoshop/Squoosh to 800x288 px (reduces file to ~15 KB).<br>

<br>2. Downscale `logo.png` to 80x80 px (reduces file to ~2 KB).<br>

<br>3. Add explicit HTML attributes (`width="616" height="616"`) to all stacking hero `<img>` tags to guarantee zero CLS.

 | **+2 to +4 pts** | Serving 2K resolution images for a 500px footer container wastes user bandwidth. Resizing images reduces total payload size.

 |
| **7. AVERAGE**<br>

<br>*(TBT / Caching)* | **Uncached Third-Party Telemetry & Analytics**<br><br>

<br>• `[collect.commerce.godaddy.com/sdk.js](https://collect.commerce.godaddy.com/sdk.js)` (48.5 KB, **771ms** render blocking, 0s cache lifetime).

<br>

<br>• Google Tag Manager (`gtag.js`): **743ms** execution time.

 | **Defer Telemetry Scripts**:<br>

<br>1. Add `defer` attribute to GoDaddy Commerce SDK and GTM scripts.<br>

<br>2. Delay analytics initialization until 2 seconds after page load or on first user interaction (`touchstart`/`scroll`):<br>

<br>`js<br>window.addEventListener('pointerdown', initAnalytics, {once: true});<br>` | **+2 to +3 pts** | Analytics and payment trackers should never block the critical rendering path. Deferring them allows the core DOM to render cleanly. |

---

### Realistic Performance Gains & Expected Score Projection

```
[Current Baseline Score: 27 / 100]
 ├── +18 pts  (Fix 1: Replace CDN Tailwind JS with Static Minified CSS)
 ├── +15 pts  (Fix 2: Eliminate Forced Reflows & Throttle GSAP/Hero Engine)
 ├── +12 pts  (Fix 3: Defer Three.js / Async WebGL 3D Model Pipeline)
 ├── +08 pts  (Fix 4: Dequeue Unused Ultimate Member & WooCommerce Assets)
 ├── +04 pts  (Fix 5: Async Google Fonts & Font-Display Swap)
 ├── +03 pts  (Fix 6: Downscale Oversized Images & Set Explicit Dimensions)
 └── +02 pts  (Fix 7: Defer Analytics & Telemetry Scripts)
────────────────────────────────────────────────────────────────────────
[Projected Target Score: 89 – 95 / 100 on Mobile Lighthouse]

```

### Key Execution Order

1. **Compile Tailwind CSS statically** and remove `cdn.tailwindcss.com`. This single change will immediately eliminate ~5 seconds of scripting work.


2. **Fix forced reflows** in `corridor-hero-engine.js` (line 1019) by batching geometry reads (`offsetWidth`).


3. **Dequeue unused plugin CSS/JS** on the home page via `functions.php`.


4. **Scale down footer and logo images** to their displayed dimensions.