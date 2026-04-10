
  <title>嘴替 · Translator</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    *, *::before, *::after { box-sizing: border-box; }
    :root {
      --ease-expo:  cubic-bezier(0.19, 1, 0.22, 1);
      --ease-quart: cubic-bezier(0.76, 0, 0.24, 1);
      --ease-back:  cubic-bezier(0.34, 1.56, 0.64, 1);
    }
    body {
      font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display",
        "Helvetica Neue", Arial, sans-serif;
      -webkit-font-smoothing: antialiased;
      background: #fff; color: #000; overflow-x: hidden;
    }

    /* ── Page entrance ── */
    @keyframes pageIn {
      from { opacity:0; transform:translateY(14px); }
      to   { opacity:1; transform:translateY(0); }
    }
    .page-enter { animation: pageIn 0.65s var(--ease-expo) both; }
    .d1{animation-delay:.04s} .d2{animation-delay:.1s}
    .d3{animation-delay:.17s} .d4{animation-delay:.24s}

    /* ── Settings panel ── */
    #settingsPanel {
      max-height: 0; overflow: hidden;
      transition: max-height 0.5s var(--ease-quart), opacity 0.4s ease;
      opacity: 0;
    }
    #settingsPanel.open { max-height: 200px; opacity: 1; }

    .cfg-input {
      width: 100%; border: none; border-bottom: 1px solid #ebebeb;
      outline: none; background: transparent;
      font-size: 12px; color: #000; padding: 8px 0;
      font-family: inherit; letter-spacing: 0.02em;
      transition: border-color 0.25s ease;
    }
    .cfg-input::placeholder { color: #d0d0d0; }
    .cfg-input:focus { border-bottom-color: #000; }

    /* ── AI status dot ── */
    .status-dot {
      width: 6px; height: 6px; border-radius: 50%;
      display: inline-block; flex-shrink: 0;
      transition: background 0.3s ease;
    }
    .dot-ai     { background: #22c55e; animation: pulse 2s infinite; }
    .dot-local  { background: #d4d4d4; }
    .dot-error  { background: #ef4444; }
    @keyframes pulse {
      0%,100%{box-shadow:0 0 0 0 rgba(34,197,94,.4)}
      50%{box-shadow:0 0 0 4px rgba(34,197,94,0)}
    }

    /* ── Role tabs ── */
    .role-wrap {
      position: relative; display: flex; gap: 0;
      border-bottom: 1px solid #f0f0f0;
      overflow-x: auto; scrollbar-width: none;
    }
    .role-wrap::-webkit-scrollbar { display: none; }
    .role-tab {
      position: relative; z-index: 1;
      padding: 8px 15px; font-size: 12px; letter-spacing: 0.07em;
      color: #c0c0c0; background: none; border: none;
      cursor: pointer; font-family: inherit; white-space: nowrap;
      transition: color 0.22s var(--ease-quart); flex-shrink: 0;
    }
    .role-tab:hover { color: #666; }
    .role-tab.active { color: #000; font-weight: 500; }
    .role-indicator {
      position: absolute; bottom: -1px; height: 2px; background: #000;
      transition: left 0.38s var(--ease-quart), width 0.38s var(--ease-quart);
      pointer-events: none;
    }

    /* ── Input ── */
    textarea {
      resize: none; border: none; outline: none;
      background: transparent; caret-color: #000;
      transition: opacity 0.2s;
    }
    textarea::placeholder { color: #ddd; }
    .input-line {
      height: 1px; background: #efefef;
      transition: background 0.3s ease;
    }
    .input-wrap:focus-within .input-line { background: #000; }

    /* ── Buttons ── */
    .btn {
      cursor: pointer; font-family: inherit;
      letter-spacing: 0.1em; text-transform: uppercase;
      transition: transform 0.35s var(--ease-expo),
                  background 0.28s var(--ease-quart),
                  color 0.28s var(--ease-quart),
                  box-shadow 0.3s ease, opacity 0.2s;
      position: relative; overflow: hidden;
    }
    .btn:hover { transform: translateY(-2px); }
    .btn:active { transform: translateY(0) scale(0.98); opacity: .8; }
    .btn-solid { background: #000; color: #fff; }
    .btn-solid:hover { box-shadow: 0 8px 24px rgba(0,0,0,.16); }
    .btn-outline { background: transparent; color: #000; border: 1px solid #ccc; }
    .btn-outline:hover { background: #000; color: #fff; border-color: #000;
                         box-shadow: 0 8px 24px rgba(0,0,0,.14); }

    /* ── Divider ── */
    .vdivider { width:1px; background:#f0f0f0; align-self:stretch; margin:0 50px; flex-shrink:0; }

    /* ── Loading bars ── */
    .bar-wrap { display:flex; align-items:flex-end; gap:4px; height:22px; }
    @keyframes barBounce {
      0%,100%{transform:scaleY(.2);opacity:.2} 50%{transform:scaleY(1);opacity:1}
    }
    .bar { width:3px; height:100%; background:#ccc; transform-origin:bottom;
           animation:barBounce .9s ease-in-out infinite; }
    .bar:nth-child(2){animation-delay:.13s}
    .bar:nth-child(3){animation-delay:.26s}
    .bar:nth-child(4){animation-delay:.39s}
    .bar:nth-child(5){animation-delay:.52s}

    /* ── Result ── */
    @keyframes resultIn {
      from{opacity:0;transform:translateY(12px)}
      to{opacity:1;transform:translateY(0)}
    }
    .result-enter { animation: resultIn .5s var(--ease-expo) both; }
    .quote-mark { position:relative; padding-left:4px; }
    .quote-mark::before {
      content:'\201C'; position:absolute; top:-10px; left:-2px;
      font-size:54px; line-height:1; color:#f0f0f0;
      font-family:Georgia,serif; pointer-events:none; user-select:none;
    }

    /* ── Badge ── */
    .badge {
      font-size:9px; letter-spacing:.2em; text-transform:uppercase;
      padding:3px 9px; border:1px solid; display:inline-flex;
      align-items:center; gap:6px;
    }
    .badge::before { content:''; width:5px; height:5px; border-radius:50%; flex-shrink:0; }
    .badge-eq    { border-color:#000; color:#000; }
    .badge-eq::before { background:#000; }
    .badge-crazy { border-color:#dc2626; color:#dc2626; }
    .badge-crazy::before { background:#dc2626; animation:barBounce .7s infinite; }
    .badge-ai { border-color:#22c55e; color:#22c55e; }
    .badge-ai::before { background:#22c55e; }

    /* ── Action links ── */
    .action-link {
      font-size:10px; letter-spacing:.12em; text-transform:uppercase;
      background:none; border:none; cursor:pointer;
      padding:0; font-family:inherit; color:#c8c8c8;
      transition:color .22s ease, transform .25s var(--ease-back);
      display:inline-flex; align-items:center; gap:4px;
    }
    .action-link:hover { color:#000; transform:translateY(-1px); }

    /* ── Toast ── */
    #toast {
      position:fixed; bottom:28px; left:50%;
      transform:translateX(-50%) translateY(50px);
      background:#000; color:#fff;
      font-size:10px; letter-spacing:.15em; text-transform:uppercase;
      padding:11px 22px;
      transition:transform .45s var(--ease-expo), opacity .4s ease;
      opacity:0; pointer-events:none; white-space:nowrap;
      display:flex; align-items:center; gap:8px;
    }
    #toast::before { content:'✓'; font-size:11px; }
    #toast.show { transform:translateX(-50%) translateY(0); opacity:1; }

    /* ── Error shake ── */
    @keyframes shake {
      0%,100%{transform:translateX(0)}
      20%{transform:translateX(-6px)}
      40%{transform:translateX(6px)}
      60%{transform:translateX(-4px)}
      80%{transform:translateX(4px)}
    }
    .shake { animation:shake .4s ease; }

    #charCount { transition:color .25s ease; }

    @media(max-width:768px){
      .main-grid{flex-direction:column!important}
      .vdivider{display:none}
      .right-col{border-top:1px solid #f0f0f0;padding-top:36px;margin-top:36px}
    }
  </style>
</head>
<body>

<!-- ══════════ HEADER ══════════ -->
<header style="padding:56px 0 40px;text-align:center;position:relative;">
  <p class="page-enter d1"
     style="font-size:10px;letter-spacing:.28em;color:#c8c8c8;text-transform:uppercase;margin:0 0 10px;">
    Universal Translator
  </p>
  <h1 class="page-enter d2"
      style="font-size:28px;font-weight:600;letter-spacing:-.035em;margin:0 0 8px;">
    嘴替
  </h1>
  <p class="page-enter d3"
     style="font-size:12px;color:#c0c0c0;font-weight:300;letter-spacing:.06em;margin:0 0 20px;">
    想说的话，让我来说。
  </p>

  <!-- AI Config trigger -->
  <div class="page-enter d4" style="display:inline-flex;align-items:center;gap:8px;cursor:pointer;"
       onclick="toggleSettings()">
    <span class="status-dot dot-local" id="statusDot"></span>
    <span id="statusText"
          style="font-size:10px;letter-spacing:.12em;color:#d0d0d0;text-transform:uppercase;
                 transition:color .25s ease;"
          onmouseover="this.style.color='#000'" onmouseout="this.style.color='#d0d0d0'">
      本地模式 · 配置 AI
    </span>
  </div>
</header>

<!-- ══════════ SETTINGS PANEL ══════════ -->
<div id="settingsPanel">
  <div style="max-width:520px;margin:0 auto;padding:0 36px 32px;">
    <div style="border:1px solid #f0f0f0;padding:24px 28px;">
      <p style="font-size:9px;letter-spacing:.22em;color:#c8c8c8;text-transform:uppercase;margin:0 0 20px;">
        AI 配置 · OpenAI Compatible
      </p>
      <div style="display:grid;gap:16px;">
        <div>
          <label style="font-size:10px;color:#c0c0c0;letter-spacing:.1em;display:block;margin-bottom:6px;">
            API KEY
          </label>
          <input class="cfg-input" id="cfgKey" type="password" placeholder="sk-..." />
        </div>
        <div style="display:grid;grid-template-columns:1fr 1fr;gap:12px;">
          <div>
            <label style="font-size:10px;color:#c0c0c0;letter-spacing:.1em;display:block;margin-bottom:6px;">
              BASE URL
            </label>
            <input class="cfg-input" id="cfgBase" type="text" placeholder="https://api.openai.com/v1" />
          </div>
          <div>
            <label style="font-size:10px;color:#c0c0c0;letter-spacing:.1em;display:block;margin-bottom:6px;">
              MODEL
            </label>
            <input class="cfg-input" id="cfgModel" type="text" placeholder="gpt-4o-mini" />
          </div>
        </div>
        <div style="display:flex;gap:12px;align-items:center;padding-top:4px;">
          <button onclick="saveConfig()"
            style="font-size:10px;letter-spacing:.12em;text-transform:uppercase;
                   padding:10px 20px;background:#000;color:#fff;border:none;
                   cursor:pointer;font-family:inherit;
                   transition:opacity .2s;"
            onmouseover="this.style.opacity='.75'" onmouseout="this.style.opacity='1'">
            保存
          </button>
          <button onclick="clearConfig()"
            style="font-size:10px;letter-spacing:.12em;text-transform:uppercase;
                   padding:10px 20px;background:none;color:#c0c0c0;border:none;
                   cursor:pointer;font-family:inherit;
                   transition:color .2s;"
            onmouseover="this.style.color='#000'" onmouseout="this.style.color='#c0c0c0'">
            清除
          </button>
          <span id="cfgStatus" style="font-size:10px;color:#c0c0c0;letter-spacing:.05em;"></span>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- ══════════ MAIN ══════════ -->
<main style="max-width:1000px;margin:0 auto;padding:0 36px 90px;">
  <div class="main-grid page-enter" style="display:flex;align-items:flex-start;animation-delay:.28s;">

    <!-- ── LEFT ── -->
    <div style="flex:1;display:flex;flex-direction:column;min-width:0;">

      <p style="font-size:9px;letter-spacing:.22em;color:#d0d0d0;text-transform:uppercase;margin:0 0 14px;">
        对象 &nbsp;/&nbsp; Who
      </p>

      <!-- Role tabs -->
      <div class="role-wrap" id="roleWrap" style="margin-bottom:32px;">
        <div class="role-indicator" id="roleIndicator"></div>
        <button class="role-tab active" data-role="同事">同事</button>
        <button class="role-tab" data-role="领导">领导</button>
        <button class="role-tab" data-role="家人">家人</button>
        <button class="role-tab" data-role="伴侣">伴侣</button>
        <button class="role-tab" data-role="朋友">朋友</button>
        <button class="role-tab" data-role="老师">老师</button>
        <button class="role-tab" data-role="甲方">甲方</button>
        <button class="role-tab" data-role="室友">室友</button>
        <button class="role-tab" data-role="相亲">相亲</button>
      </div>

      <p style="font-size:9px;letter-spacing:.22em;color:#d0d0d0;text-transform:uppercase;margin:0 0 14px;">
        想法 &nbsp;/&nbsp; Input
      </p>

      <div class="input-wrap">
        <textarea id="inputText" rows="6"
          style="font-size:19px;line-height:1.7;font-weight:300;color:#000;width:100%;"
          placeholder="说说你的真实想法..."
          maxlength="300"></textarea>
        <div style="display:flex;justify-content:flex-end;margin-top:6px;">
          <span id="charCount" style="font-size:10px;color:#ddd;">0 / 300</span>
        </div>
        <div class="input-line" style="margin-top:4px;"></div>
      </div>

      <div style="display:flex;gap:10px;margin-top:22px;">
        <button class="btn btn-solid" id="btnHigh"
          style="flex:1.2;padding:15px 10px;font-size:11px;font-weight:500;">
          一键高情商
        </button>
        <button class="btn btn-outline" id="btnCrazy"
          style="flex:1;padding:15px 10px;font-size:11px;font-weight:500;">
          彻底发疯
        </button>
      </div>

      <p style="font-size:10px;color:#e0e0e0;margin-top:12px;letter-spacing:.03em;">
        Enter 翻译 &nbsp;·&nbsp; Shift+Enter 换行
      </p>
    </div>

    <!-- ── Divider ── -->
    <div class="vdivider"></div>

    <!-- ── RIGHT ── -->
    <div class="right-col" style="flex:1;display:flex;flex-direction:column;min-width:0;">
      <p style="font-size:9px;letter-spacing:.22em;color:#d0d0d0;text-transform:uppercase;margin:0 0 14px;">
        翻译 &nbsp;/&nbsp; Output
      </p>

      <div id="statePlaceholder">
        <p style="font-size:19px;font-weight:300;color:#e8e8e8;line-height:1.7;margin:0;">
          翻译结果在这里出现...
        </p>
      </div>

      <div id="stateLoading" style="display:none;padding-top:8px;">
        <div class="bar-wrap">
          <div class="bar"></div><div class="bar"></div><div class="bar"></div>
          <div class="bar"></div><div class="bar"></div>
        </div>
        <p id="loadingText"
           style="font-size:11px;color:#d8d8d8;margin:10px 0 0;letter-spacing:.06em;">
          正在翻译...
        </p>
      </div>

      <div id="stateResult" style="display:none;flex-direction:column;">
        <div id="modeBadge" style="margin-bottom:20px;"></div>
        <div class="quote-mark" style="min-height:80px;">
          <p id="resultText"
             style="font-size:19px;font-weight:300;line-height:1.8;margin:0;color:#000;word-break:break-all;">
          </p>
        </div>
        <div style="margin-top:28px;padding-top:18px;border-top:1px solid #f5f5f5;
                    display:flex;gap:20px;align-items:center;">
          <button class="action-link" id="copyBtn" onclick="copyResult()">
            复制 <span style="font-size:12px;">↗</span>
          </button>
          <button class="action-link" onclick="resetAll()">重新来过</button>
          <button class="action-link" onclick="translate(lastMode)" style="margin-left:auto;">
            换一条 ↺
          </button>
        </div>
      </div>
    </div>
  </div>

  <!-- Footer -->
  <div style="margin-top:80px;display:flex;align-items:center;gap:20px;">
    <div style="flex:1;height:1px;background:#f5f5f5;"></div>
    <span style="font-size:9px;letter-spacing:.18em;color:#e2e2e2;white-space:nowrap;">@zeyi</span>
    <div style="flex:1;height:1px;background:#f5f5f5;"></div>
  </div>
</main>

<div id="toast">已复制到剪贴板</div>

<script>
/* ═══════════════════════════════════════════════════════
   CONFIG
═══════════════════════════════════════════════════════ */
let cfgKey   = localStorage.getItem('zr_key')   || '';
let cfgBase  = localStorage.getItem('zr_base')  || 'https://api.openai.com/v1';
let cfgModel = localStorage.getItem('zr_model') || 'gpt-4o-mini';

function initCfgUI() {
  document.getElementById('cfgKey').value   = cfgKey;
  document.getElementById('cfgBase').value  = cfgBase;
  document.getElementById('cfgModel').value = cfgModel;
  updateStatusUI();
}

function toggleSettings() {
  const p = document.getElementById('settingsPanel');
  p.classList.toggle('open');
}

function saveConfig() {
  cfgKey   = document.getElementById('cfgKey').value.trim();
  cfgBase  = document.getElementById('cfgBase').value.trim() || 'https://api.openai.com/v1';
  cfgModel = document.getElementById('cfgModel').value.trim() || 'gpt-4o-mini';
  localStorage.setItem('zr_key',   cfgKey);
  localStorage.setItem('zr_base',  cfgBase);
  localStorage.setItem('zr_model', cfgModel);
  updateStatusUI();
  const s = document.getElementById('cfgStatus');
  s.textContent = '已保存'; s.style.color = '#22c55e';
  setTimeout(() => { s.textContent = ''; }, 2000);
}

function clearConfig() {
  cfgKey = ''; cfgBase = 'https://api.openai.com/v1'; cfgModel = 'gpt-4o-mini';
  localStorage.removeItem('zr_key');
  document.getElementById('cfgKey').value = '';
  updateStatusUI();
  const s = document.getElementById('cfgStatus');
  s.textContent = '已清除'; s.style.color = '#d0d0d0';
  setTimeout(() => { s.textContent = ''; }, 2000);
}

function updateStatusUI() {
  const dot  = document.getElementById('statusDot');
  const text = document.getElementById('statusText');
  if (cfgKey) {
    dot.className  = 'status-dot dot-ai';
    text.textContent = 'AI 模式 · ' + cfgModel;
  } else {
    dot.className  = 'status-dot dot-local';
    text.textContent = '本地模式 · 配置 AI';
  }
}

/* ═══════════════════════════════════════════════════════
   ROLE CONFIG
═══════════════════════════════════════════════════════ */
const ROLE_CONTEXT = {
  同事: '平级同事',
  领导: '直属领导或上级',
  家人: '父母或家人',
  伴侣: '男女朋友或配偶',
  朋友: '普通朋友',
  老师: '老师或教授',
  甲方: '甲方客户',
  室友: '合租室友',
  相亲: '相亲对象（第一次见面）',
};

const PLACEHOLDERS = {
  同事: '比如：这个需求改了八百遍了我不想改了...',
  领导: '比如：凭什么加班又是我...',
  家人: '比如：别再催我结婚了...',
  伴侣: '比如：你根本就不理解我...',
  朋友: '比如：你只有有事才找我...',
  老师: '比如：这门课我完全听不懂但不敢说...',
  甲方: '比如：改了这么多版还要改我要崩了...',
  室友: '比如：能不能不要半夜洗澡...',
  相亲: '比如：没感觉想提前结束...',
};

/* ═══════════════════════════════════════════════════════
   AI CALL
═══════════════════════════════════════════════════════ */
async function callAI(input, mode) {
  if (!cfgKey) return null;

  const roleDesc = ROLE_CONTEXT[currentRole] || currentRole;

  const sys = `你是一个精通中国人际沟通的助手，擅长帮人把话说得更得体或更戏剧化。
回复规则：
- 只输出翻译结果本身，不加任何解释、前缀或引号
- 语言自然，像真人说话
- 简短有力`;

  const userPrompt = mode === 'high'
    ? `把这句话翻译成高情商版本，说话对象是【${roleDesc}】。

原话：${input}

要求：
- 保留核心意思，表达更成熟得体
- 口语化，绝不用"您"、不说官话废话
- 根据对象关系调整语气和措辞
- 1-2句话，不超过50字
- 只输出翻译结果`

    : `把这句话翻译成发疯版本，说话对象是【${roleDesc}】。

原话：${input}

要求：
- 极度夸张，戏剧性十足，有崩溃感
- 用大量！！！，情绪饱满
- 结合对象关系，发疯有针对性，接地气
- 2-3句话
- 只输出发疯内容`;

  const base = cfgBase.replace(/\/$/, '');
  const res = await fetch(`${base}/chat/completions`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${cfgKey}`,
    },
    body: JSON.stringify({
      model: cfgModel,
      messages: [
        { role: 'system', content: sys },
        { role: 'user',   content: userPrompt },
      ],
      max_tokens: 200,
      temperature: mode === 'crazy' ? 1.15 : 0.9,
    }),
  });

  if (!res.ok) {
    const err = await res.json().catch(() => ({}));
    throw new Error(err?.error?.message || `HTTP ${res.status}`);
  }
  const data = await res.json();
  return data.choices?.[0]?.message?.content?.trim() || null;
}

/* ═══════════════════════════════════════════════════════
   LOCAL FALLBACK CORPUS
   每个 r 数组足够大，同一关键词多次触发不重复
═══════════════════════════════════════════════════════ */
const LOCAL = {
  high: {
    同事: {
      kw: [
        { t:['改','重做','推翻','修改','重来'], r:[
          '改没问题，先把最终标准对齐一下？',
          '可以改，这次的优先级和截止是什么？',
          '没问题，改之前先确认需求不再变？',
          '行，这版什么地方改，你说具体一点。',
          '我来调，你先把意见整理一下发我。',
          '改可以，给我一个时间节点。',
          '好，这次改完验收标准是什么？',
          '没问题，我这边过一遍，有问题找你。',
        ]},
        { t:['加班','今晚','周末','通宵','节假日','假期'], r:[
          '今晚有事，明早第一件事跟进。',
          '周末不方便，工作日能排开的。',
          '我手里还有几件，能说一下哪个最紧吗？',
          '今天走不开，我看看能不能调一下。',
          '可以留，但后面能申个调休吗？',
          '这个时间有安排，我们看工作日有没有档期。',
          '我来想想怎么安排，给你个确定的回复。',
          '今晚加不了，我明天早来把这块补上。',
        ]},
        { t:['催','什么时候','ddl','deadline','进度','提交','什么进展'], r:[
          '在推了，有进展马上同步你。',
          '今天下班前发你，稍等。',
          '卡了一个地方，解决了立刻给你。',
          '80% 了，快了，今天内给你。',
          '预计下午三点，到了提醒你。',
          '我这边还在跑，有结果第一时间发。',
          '快了，就差最后一步，等我消息。',
          '不会超今天，我盯着呢。',
        ]},
        { t:['锅','背锅','甩锅','不是我','责任','怪我'], r:[
          '我来梳理一下过程，把时间线对齐。',
          '说清楚比较好，咱们一起复盘一下。',
          '我整理一份记录，谁负责哪段一目了然。',
          '这块我们再看一下分工，别说不清楚。',
          '我来拉个对齐会，把责任边界搞清楚。',
          '来，复盘一下，怎么产生的怎么避免。',
          '这件事我建议我们单独说清楚，别带着走。',
          '我把邮件记录拉出来，大家一起看。',
        ]},
        { t:['你来','你做','你负责','交给你','帮我做'], r:[
          '这块不在我范围里，要不先拉上 leader 确认？',
          '我手里比较满，排期确认了再说。',
          '我可以协助，但主责得先定清楚是谁。',
          '这个要走正式流程吗，还是口头分工？',
          '我来做没问题，需求得给完整。',
          '排期上我先放着，你给我个优先级。',
          '这个我接了，但得给我时间，你能等吗？',
          '说一下背景，我评估一下能不能接。',
        ]},
        { t:['会议','开会','拉会','讨论','汇报'], r:[
          '能提前发一下议题吗，方便我准备。',
          '哪几个人必须到场，时间多久？',
          '这个用邮件能对齐吗，不然我排一下。',
          '时间定好我来安排，你发个日历邀请。',
          '我这边什么时间都行，你定。',
          '会议目的是决策还是对齐，我准备一下。',
          '我来拉，你先确认一下谁需要参加。',
          '这个半小时能说完吗，我们控一下时间。',
        ]},
        { t:['不合理','太多了','受不了','做不完','不公平','凭什么'], r:[
          '我理解，能聊一下优先级怎么排吗？',
          '我想了解一下这次分工的逻辑是什么。',
          '我手里确实比较满，能说一下哪个最紧急？',
          '我有点顾虑，找个时间说一下？',
          '这块我需要一些支持，有资源可以协调吗？',
          '我们对一下工作量，我怕质量出问题。',
          '我有个不同想法，方便聊一下吗？',
          '说实话这个量有点大，有没有可以调的部分？',
        ]},
      ],
      def:[
        '我确认一下，回头同步你。',
        '这个点我有不同看法，能聊一下吗？',
        '我理解你的意思，但我有点顾虑。',
        '排期有点紧，能往后调吗？',
        '需要一些上下文，能补充一下吗？',
        '先记下来，我跟进一下。',
        '我看一下，有情况马上找你。',
        '这块我不确定，先确认再推。',
        '能给我发一下相关背景吗？',
        '好，我来跟，有问题找你。',
        '这个我来处理，你先忙别的。',
        '我整理一下思路，找你再说。',
        '可以，给我今天结束前的时间。',
        '没问题，我排进去。',
        '说一下期望的结果是什么？',
      ]
    },

    领导: {
      kw: [
        { t:['改','重做','推翻','方向不对','重来'], r:[
          '收到，能说一下期望的方向吗？',
          '好，改之前能对齐一下核心标准吗？',
          '明白，这次的重点是哪几块？',
          '收到，我重新梳理一版给您确认。',
          '好的，参考案例有吗，方便我对标。',
          '收到反馈，预计今晚给您一版。',
          '明白，我先改这部分，其他不动您看一下。',
          '好，我来调，有问题随时找您。',
        ]},
        { t:['加班','今晚','周末','赶一下','紧急','明天早上'], r:[
          '好的，有卡点提前报备您。',
          '收到，今晚完成，明早发您。',
          '没问题，时间节点是？',
          '收到，优先处理这个。',
          '行，我这边重新排一下，今天给您。',
          '好，我来安排，您放心。',
          '收到，我先把这个顶上来。',
          '没问题，有情况随时联系我。',
        ]},
        { t:['催','进度','怎么还没','什么时候','结果呢'], r:[
          '收到，马上同步最新进展给您。',
          '正在推，今天下班前给您一版。',
          '卡了个问题，我来解决，稍等。',
          '给我两小时，给您一个完整版。',
          '马上，我整理一下立刻发您。',
          '快了，差最后一步，今天内给您。',
          '已经在跑了，有结果第一时间汇报。',
          '我这边再跟进一下，一小时内反馈您。',
        ]},
        { t:['晋升','涨薪','升职','调薪','评级','绩效','kpi'], r:[
          '想找个时间跟您聊聊我的发展方向。',
          '我整理了一份工作成果，方便的时候汇报？',
          '想听听您对我下一步发展的建议。',
          '我对自己这段时间的工作有一些总结，能交流一下吗？',
          '想了解一下对我的期待和评估维度。',
        ]},
        { t:['不合理','太多','超量','资源','人手不够'], r:[
          '想跟您沟通一下工作量，看优先级怎么排。',
          '我把当前任务列表发给您看一下？',
          '需要您帮确认一下哪个最优先，我来调整。',
          '资源上能支持一下吗，我怕影响质量。',
          '这块我有个顾虑，方便说一下吗？',
        ]},
      ],
      def:[
        '收到，尽快落实。',
        '明白，确认细节再推进。',
        '好，有情况第一时间汇报。',
        '需要确认一个前提，麻烦给个方向。',
        '我来安排，您放心。',
        '明白了，我来处理。',
        '收到，跟进一下，有结果马上报。',
        '好，今天内给您一个反馈。',
        '了解，按这个方向推。',
        '收到，对齐一下相关方。',
        '好的，我整理一下来汇报。',
        '明白，优先级我重新排一下。',
        '没问题，我这边安排上了。',
        '收到，我来跟进。',
        '了解，我先确认一下再给您回复。',
      ]
    },

    家人: {
      kw: [
        { t:['催婚','结婚','对象','找对象','成家','单身'], r:[
          '这件事我有自己的节奏，不用担心。',
          '我在认真对待，有进展会告诉你们。',
          '缘分急不来，到时候第一个通知你们。',
          '你们着急我理解，但这个我来。',
          '我不是不想，是没遇到合适的。',
          '感情的事强求不了，给我一点时间。',
          '我有在认真找，你们信任我。',
          '这件事我很清楚，不用替我着急。',
        ]},
        { t:['催生','生孩子','二胎','生娃'], r:[
          '这个我们有自己的计划，到时候会说。',
          '时机没到，我们在考虑，不急。',
          '这件事很重要，我们得想清楚再决定。',
          '你们放心，我们有在认真考虑。',
        ]},
        { t:['钱','借钱','给钱','转我','打款','支援'], r:[
          '我手头也不宽裕，这次帮不上，不好意思。',
          '最近比较紧，实在难。',
          '我自己也有压力，这次给不了。',
          '我没有多余的，真的很抱歉。',
          '等我宽裕了一定支持，现在真的难。',
        ]},
        { t:['回家','不回来','怎么不回','假期多久'], r:[
          '最近太忙走不开，有空一定回。',
          '我安排一下，尽量回。',
          '我想回，但真的走不开，下次补上。',
          '我看看时间，确定了第一时间告诉你们。',
          '下次回去好好陪你们，这次真的走不开。',
        ]},
        { t:['工资','赚多少','多少钱','工作怎么样','混得怎么样'], r:[
          '还好，够花，别担心我。',
          '正在努力，给我点时间。',
          '能养活自己，你们放心。',
          '还行，不用操心这个。',
          '在努力，会越来越好的。',
        ]},
        { t:['管','干涉','听话','怎么这样','不孝','叛逆'], r:[
          '我知道你们是为我好，但这件事让我来。',
          '我不是不听，我有自己的考虑。',
          '我是你们孩子，但我有自己的判断。',
          '我们能好好聊吗，别这样说。',
          '我爱你们，但这个得我自己决定。',
          '我听到了，我会考虑的。',
          '你们的心意我感受到了，但方式可以商量。',
          '我没有不在意你们，我只是需要空间。',
        ]},
      ],
      def:[
        '我知道你们是为我好。',
        '给我点时间，我会的。',
        '这件事我有想法，信任我。',
        '我很好，不用担心我。',
        '让我自己来，我心里有数。',
        '我听到了，但我需要自己决定。',
        '我爱你们，但这个我自己扛。',
        '我在努力，你们也要保重。',
        '不用操心，我照顾好自己了。',
        '有什么事我会说的，你们放心。',
        '我明白你们的意思，我来想办法。',
        '家里有什么事告诉我，我能帮就帮。',
        '我很好，真的，放心吧。',
        '好好的，别担心。',
        '我回头跟你们说，现在先这样。',
      ]
    },

    伴侣: {
      kw: [
        { t:['分手','不想了','算了','结束','不在一起','走'], r:[
          '我们现在都有情绪，先冷静一下再聊？',
          '这个词我不想说，我们说说到底发生了什么。',
          '我很在乎我们，所以更需要认真谈，别冲动。',
          '给我一点时间，这件事我没想好。',
          '我不想就这么结束，能给我们一次认真谈的机会吗？',
          '我现在说不好，等情绪平了我们再好好聊。',
        ]},
        { t:['不回','没回','已读','不接','不理我','消失了'], r:[
          '我刚才需要缓一缓，不是不理你。',
          '当时状态不好，不是故意不回的。',
          '我没有消失，只是需要安静一下。',
          '我在的，刚才走不开，现在说？',
          '不是不想回，就是那会儿说不了话。',
          '给我一点空间不代表我不在乎你。',
        ]},
        { t:['生气','什么态度','怎么了','脸色','情绪'], r:[
          '我不是冲你的，我只是今天很累。',
          '有情绪但不是针对你，让我缓一缓。',
          '我现在状态不好，不是因为你。',
          '我没生你气，只是需要安静一会儿。',
          '我很累，不是不想理你，给我一点时间。',
          '我的情绪跟你无关，但我需要你别追问。',
        ]},
        { t:['不在乎','不关心','不爱了','心不在','敷衍','装'], r:[
          '我很在乎你，只是不太会表达。',
          '不是不关心，是我最近状态不好，但我爱你。',
          '你告诉我你需要什么，我来改。',
          '我爱你，但我也需要空间，这两者不矛盾。',
          '我需要你告诉我你感受到了什么，我来解释。',
          '你对我很重要，这没有变过。',
        ]},
        { t:['吃醋','嫉妒','那个人','说清楚','解释一下'], r:[
          '我需要你说一下发生了什么，我有点不安全感。',
          '我不是不信任你，我只是需要安慰。',
          '你说一下，我听，我想理解。',
          '我在意这件事，能说清楚吗？',
          '我的感受说一下可以吗，我有点在意。',
        ]},
        { t:['冷战','不说话','沉默','不理我','消极对抗'], r:[
          '我不想冷战，这样解决不了问题，我们说出来。',
          '我想跟你说话，能回应我吗？',
          '冷战让我很难受，我宁愿吵架。',
          '我们都别憋着，说出来才能解决。',
        ]},
        { t:['你的错','都怪你','是你','你的问题','你先'], r:[
          '你说哪里让你不开心，我来听。',
          '我想理解你的感受，你说具体一点。',
          '告诉我我哪里做得不好，我来改。',
          '好，我听，你说。',
          '我愿意承认问题，但我需要知道是哪里。',
        ]},
        { t:['太忙','没时间','忽略我','不陪我','工作优先'], r:[
          '你说得对，我最近忽略你了，对不起。',
          '我来想办法，我们约一个只属于我们的时间。',
          '不是不想陪你，我们一起排个时间？',
          '我听到了，我会调整的，谢谢你告诉我。',
        ]},
      ],
      def:[
        '我不是不在乎你，只是现在说不好。',
        '我们能不吵，好好说吗？',
        '给我一点时间，整理好了来找你。',
        '我很珍视我们，所以才在意这些。',
        '你对我很重要，这没有变。',
        '我们能聊一下吗，我有话想说。',
        '我在，你说，我听着。',
        '我需要你，但也需要你理解我。',
        '我们好好谈吧，我不想这样。',
        '今晚能说说吗，我有点憋着。',
        '别生气，我没有你想的那个意思。',
        '我爱你，这件事不影响这个。',
        '你说得有道理，我来想想怎么改。',
        '我一直都在，没有走。',
        '让我们都冷静一下，再来谈？',
      ]
    },

    朋友: {
      kw: [
        { t:['借钱','借我','周转','还没还','欠我'], r:[
          '最近我也比较紧，这次帮不上，不好意思。',
          '钱的事真的帮不上，怕影响我们。',
          '我手头也就够自己用，没法借。',
          '这次真没法，不是不想帮你。',
          '说实话我不宽裕，你找别人问一下？',
        ]},
        { t:['放鸽子','没来','失约','临时取消','不来'], r:[
          '今天状态真的不行，改天我请你。',
          '不好意思，有急事，下次你定地方。',
          '对不起，我失约了，怎么补偿你？',
          '临时有事，真的不好意思，下次一定到。',
          '我错了，下次你说怎么补，我都行。',
        ]},
        { t:['只找我','索取','利用','工具人','有事才'], r:[
          '我需要跟你说一下，我有点力不从心了。',
          '我们能聊一下吗，我有些感受想说。',
          '朋友是双向的，我也需要被照顾的。',
          '我很在乎我们的友谊，所以才说。',
          '说实话我有点累，能聊聊吗？',
        ]},
        { t:['说出去','告诉别人','秘密','扩散','背后说'], r:[
          '那件事能帮我保密吗，很重要。',
          '能就我们知道吗，我不想扩散。',
          '麻烦别提这件事，谢谢你。',
          '这是我私事，不想被人知道，你帮我守着。',
        ]},
        { t:['不理','生气','误会','闹矛盾','你怎么了'], r:[
          '我没有你想的那个意思，我们说一下？',
          '我最近状态不好，真的不是针对你。',
          '有误会说出来，别憋着，我们很熟了。',
          '我很重视我们，别让这件事成心结。',
          '我没有想复杂，你说一下你感受到了什么。',
        ]},
        { t:['发疯','崩溃','受不了','太难了','撑不住'], r:[
          '说吧，我听，不评判你。',
          '今天先发泄，明天再想怎么办。',
          '你不是一个人，我在。',
          '需要我做什么告诉我，我来。',
          '先崩一会儿，崩完我们再想办法。',
          '我在这，你说，随便说。',
        ]},
      ],
      def:[
        '我理解你，但这次真的帮不上。',
        '我最近状态不太好，给我点空间。',
        '你说得有道理，我有点不同想法。',
        '我在，你说吧。',
        '这事我们晚点聊，现在不是时候。',
        '我支持你，但你确定要这样做吗？',
        '给我点时间想想怎么帮你。',
        '说实话也没关系，我们是朋友。',
        '你不用一个人扛，有我。',
        '放心，我不会评判你的。',
        '我听你说，不急，慢慢说。',
        '有啥事直接说，别绕弯子。',
        '我不知道说什么，但我陪着你。',
        '你辛苦了，真的。',
        '这件事换我也会崩的，正常。',
      ]
    },

    老师: {
      kw: [
        { t:['没交','没写','作业','没做','忘了','忘交'], r:[
          '老师对不起，这次没按时完成，我来补。',
          '不好意思，我失误了，能给我补交机会吗？',
          '我知道我不该，今天能交吗？',
          '对不起，我今天交可以吗？',
          '这次是我的问题，我马上补。',
        ]},
        { t:['成绩','分数','挂科','没及格','考砸了','不理想'], r:[
          '老师我想了解一下哪里扣分了，能看看吗？',
          '这次考得不理想，您有什么建议吗？',
          '我想改进，能给我一些方向吗？',
          '有补考机会吗，我想再试一次。',
          '我来看看问题出在哪，方便问您吗？',
          '我想认真对待这门课，能说说我应该怎么调整吗？',
        ]},
        { t:['旷课','逃课','没来','缺席','点名'], r:[
          '老师不好意思，那次有特殊情况，我补笔记了。',
          '我缺席了，能说一下那节课的重点吗？',
          '对不起，下次一定到。',
          '我不是故意的，那次情况特殊，我来补。',
        ]},
        { t:['听不懂','不会','不明白','没学会','看不懂','困惑'], r:[
          '这里我理解得不够，方便再解释一下吗？',
          '我卡在这个点，请问能说一下吗？',
          '您有推荐的资料吗，我自己再研究一下。',
          '课下能来问您吗，我有几个地方不太清楚。',
          '我理解了大方向，但这个细节我有疑问。',
          '我反复看了，还是没想通，能给个思路吗？',
        ]},
        { t:['论文','报告','答辩','截止','选题'], r:[
          '我想提前请教一下方向，方便吗？',
          '有几个问题想确认一下，能占用您几分钟吗？',
          '能帮我看一下大纲方向对不对吗？',
          '我整理了一个提纲，方便的话您帮我看看？',
        ]},
        { t:['迟到','来晚了','晚了'], r:[
          '老师对不起，来晚了，不会再有下次。',
          '抱歉打扰了，我先坐下。',
          '老师不好意思，来晚了。',
        ]},
      ],
      def:[
        '谢谢老师，我记下了，认真改进。',
        '老师说得对，是我没做好。',
        '我回去再想想，有问题来请教您。',
        '我理解您的意思，我来调整。',
        '谢谢您的反馈，对我很有帮助。',
        '我会认真对待这门课的。',
        '收到，按这个方向来。',
        '谢谢老师抽时间解答。',
        '我会加把劲的，谢谢您。',
        '明白了，感谢老师指导。',
        '我来好好准备，不让您失望。',
        '您这么说让我清晰多了，谢谢。',
        '我需要再消化一下，之后可以来找您吗？',
        '我认真听了，回去复习一下。',
        '谢谢老师，我去试试看。',
      ]
    },

    甲方: {
      kw: [
        { t:['改','重做','方向不对','不满意','不好','不喜欢','重来'], r:[
          '收到，能说一下哪里改，更精准一些？',
          '明白，这次的核心诉求是什么？',
          '好，改之前确认一下最终方向？',
          '收到反馈，预计今天出新版给您。',
          '可以改，有参考案例吗，方便我对标。',
          '能多说几个字吗，改起来更准。',
          '收到，我理解了，今天内给您。',
          '好的，这版哪个地方先改，我们排个序。',
        ]},
        { t:['加急','今晚','明天','赶紧','快点','尽快','越快越好'], r:[
          '好的，最快今晚给您一版，需求不变对吗？',
          '收到，加急，几点前需要？',
          '没问题，优先处理这个，今天给您。',
          '加急可以，范围确认一下不变？',
          '好，我重新排期，今晚发您。',
          '收到，我来冲一下，有问题随时联系。',
        ]},
        { t:['便宜','少一点','打折','预算','免费','优惠','降价'], r:[
          '我们对一下需求和预算，找个平衡点。',
          '能说说哪块可以缩减吗，报价是按范围来的。',
          '我理解预算压力，我们聊聊有没有别的方案。',
          '预算和效果我们一起来对，看哪个合适。',
          '能说一下预算范围吗，我们来做方案。',
        ]},
        { t:['版权','归属','使用权','知产','能不能用'], r:[
          '合同里有约定，我来给您确认一下。',
          '这块需要正式确认，要走合同流程吗？',
          '我来查一下协议条款，今天内给您回复。',
        ]},
        { t:['付款','尾款','打款','结款','钱什么时候'], r:[
          '能确认一下款项的时间节点吗？',
          '合同约定的时间快到了，麻烦安排一下？',
          '我这边跟进一下付款进度，您方便吗？',
          '能给个预计打款时间吗，我这边好安排。',
        ]},
        { t:['别人家','竞品','同行','人家','为什么他们'], r:[
          '能把那个案例发给我看看吗？',
          '我了解一下，我们对比分析一下。',
          '参考可以，我们看看更适合您的方向是什么。',
          '那个我了解，我们也可以往这个方向调整。',
        ]},
      ],
      def:[
        '收到，整理一下给您回复。',
        '明白，尽快跟进。',
        '好的，有结果马上同步您。',
        '需求我记下了，给我一点时间整理方案。',
        '收到，对齐一下再动。',
        '没问题，今天内回复您。',
        '好，我给您一个时间节点。',
        '收到您的反馈，我来处理。',
        '明白需求，安排上了。',
        '收到，我整理后发您确认。',
        '好，我这边推进，有问题随时联系。',
        '没问题，我来跟。',
        '收到，稍等，我确认一下。',
        '好的，方案我整理好了发您看。',
        '明白，我按这个推进。',
      ]
    },

    室友: {
      kw: [
        { t:['吵','噪音','声音','太响','打扰','喧闹'], r:[
          '能小声一点吗，我这边有点影响。',
          '不好意思提，能注意一下音量吗？',
          '声音稍微小点，不是不让你玩。',
          '我需要安静一下，能戴耳机吗？',
          '说一声，太响了，帮个忙。',
        ]},
        { t:['脏','乱','不打扫','公区','厨房','卫生','垃圾'], r:[
          '公区的卫生我们能约定一下吗？',
          '能一起做个值日表吗，轮流打扫。',
          '那个地方能收拾一下吗，我有点在意。',
          '说一声不是挑剔，住得舒服就好。',
          '垃圾能处理一下吗，气味有点大。',
          '厨房能用完清理一下吗？我们轮着来。',
        ]},
        { t:['晚归','深夜','开灯','半夜','很晚','凌晨'], r:[
          '晚回来轻一点，我睡眠浅。',
          '开灯没问题，能用小灯吗？',
          '回来说一声就行，方便我知道。',
          '不是不行，就是动静小一点。',
        ]},
        { t:['用我','拿我','没问','借','我的东西'], r:[
          '用我的东西说一声就行，我不介意。',
          '借东西提前问一下，不是不给用。',
          '下次说一声，我们都方便。',
          '我的东西你用没关系，知会一声就好。',
        ]},
        { t:['客人','带人','朋友来','有人'], r:[
          '带朋友来没问题，提前说一声方便我安排。',
          '说一声就行，不用这么客气。',
          '来没问题，我这边有需要会跟你说。',
        ]},
      ],
      def:[
        '说一声就行，我们好商量。',
        '住一起难免有摩擦，说出来比较好。',
        '我不是挑剔，说出来大家都舒服。',
        '我们约定一下，都方便。',
        '不是大事，说一下就解决了。',
        '我理解，找个都接受的方式。',
        '住一起就是要互相体谅。',
        '没关系，以后注意就行。',
        '不用放心上，说出来就好。',
        '我们好好的，有事说事。',
        '平时没关系，就这件事说一下。',
        '我不是针对你，就是说一声。',
        '你也有你的习惯，我理解，我们对一下。',
        '大家住得舒服最重要。',
        '没有别的意思，说出来好过憋着。',
      ]
    },

    相亲: {
      kw: [
        { t:['没感觉','不喜欢','不合适','不想发展','没意思'], r:[
          '感谢这次见面，我觉得我们可能不太合适。',
          '挺好的人，但感觉不是那个方向。',
          '坦诚说，我觉得我们更适合做朋友。',
          '真诚说，我感觉没有走下去的感觉。',
          '我觉得缘分不够，不想耽误你。',
        ]},
        { t:['条件','要求','标准','必须','一定要','硬性'], r:[
          '我觉得人合适最重要，条件是其次。',
          '比起条件，我更在意相处的感觉。',
          '我没那么多硬条件，聊聊就知道。',
          '我希望是自然相识，不用太刻意。',
        ]},
        { t:['工资','收入','多少钱','买房','有车','资产','存款'], r:[
          '这些可以慢慢了解，先看感觉。',
          '物质重要，但不是唯一，我更在意人本身。',
          '这些条件我没有硬要求，感情第一位。',
          '慢慢了解吧，第一次见面不急聊这个。',
        ]},
        { t:['催','表态','什么意思','有没有意向','喜欢吗'], r:[
          '我喜欢慢慢来，给我们一点时间了解。',
          '还在观察，不急着下结论。',
          '感情急不了，再见几次？',
          '我需要多相处才知道，不着急表态。',
        ]},
        { t:['家里催','父母','介绍','逼我来'], r:[
          '家里催得急，但我希望是自然发展。',
          '不想因为催就随便，感情还是要认真对待。',
          '我来是认真的，不是应付家里。',
        ]},
        { t:['继续','见面','再约','发展','考虑一下'], r:[
          '我愿意继续了解，什么时候方便？',
          '感觉还不错，多见几次吧。',
          '可以啊，你定时间和地方。',
          '挺好的，继续聊聊。',
        ]},
      ],
      def:[
        '挺好的，慢慢了解吧。',
        '第一次见面，放松聊就好。',
        '感情的事不着急，慢慢来。',
        '相处舒不舒服最重要。',
        '多见几次才能了解，这次是开始。',
        '你有什么想了解的，直接问。',
        '坦诚比表现重要，你觉得呢？',
        '缘分这件事，顺其自然。',
        '我希望找个能说实话的人。',
        '挺好的，比我想的轻松。',
        '先不想太多，聊聊就好。',
        '我挺享受这次聊天的。',
        '你是个很真实的人，挺好的。',
        '有缘再见，如果你也觉得不错的话。',
        '我觉得我们可以再约一次。',
      ]
    },
  },

  crazy: {
    同事: [
      '我已经是电量0%的手机了！！！还在被要求打视频！！！',
      '什么叫一个人干三个人的活！！！这就叫！！！',
      '我的理智已经提前下班！！！没有交接直接跑路！！！',
      '好的没问题！！！（内心爆炸第七次了）！！！',
      '我的黑眼圈是全公司最深的！！！这是我唯一的成就！！！',
      '你们知道我精神状态吗！！！是薯片！！！被踩扁的！！！碎了！！！',
      '我哪里有时间！！！时间全被榨走了！！！',
      '我已经麻了！！！神经死了情绪也死了只有手指在动！！！',
      '正常上班不行吗！！！为什么！！！谁来告诉我为什么！！！',
      '我要辞职！（但不敢）要躺平！（但有贷款）啊啊啊啊啊！！！',
      '我的工位是我在这个城市唯一的落脚点！！！',
      '今天崩溃三次！！！无声崩溃！含泪微笑崩溃！当众崩溃！！！',
      '需求改了多少版了！！！脑子已经糊成一锅粥！！！',
      '我是来上班还是来挑战人类极限的！！！有人能告诉我吗！！！',
      '我快不行了！！！就差没有人把我扛出去！！！',
      '明天还要来这里！！！想想就绝望！！！但还是会来的！！！',
      '说好的下班时间！！！是个谎言！！！',
      '我的周末在哪里！！！找不到了！！！报警了！！！',
      '做完这件再来一件！！！洗完这碗再有一盆！！！永无止境！！！',
      '谁发明的加班！！！站出来！！！',
    ],
    领导: [
      '好的！！！（说不行也没用）！！！',
      '收到！！！（我要原地爆炸了）！！！',
      '没问题！！！（问题大了去了）！！！',
      '我就是头牛！！！使劲薅！！！随便薅！！！',
      '行！！！加班就加！！！命不要了！！！',
      '您说的对！！！（内心十万头羊驼飞奔而过）！！！',
      '好嘞！！！（双手在抖但还是接下来了）！！！',
      '随时都行！！！（睡觉时间不算时间吗）！！！',
      '收到！！！我已经忘了正常下班是什么感觉！！！',
      '听您的！！！（但我内心在无声哭泣）！！！',
      '没有做不到！！！只有累死了！！！',
      '我来！！！反正也没别人！！！',
      '好的没问题！！！（泪水在眼眶里转圈圈）！！！',
      '明白！！！完全不明白凭什么又是我！！！但明白！！！',
      '您放心！！！（我可放不了心）！！！',
      '我来做！！！我是万能的！！！我什么都能做！！！累死活该！！！',
      '收到！！！我的休息时间向您致敬！！！',
      '行！！！这都行！！！还有什么是不行的！！！',
      '好！没问题！全搞定！！！（内心已经罢工）！！！',
      '谢谢您的信任！！！我宁愿您不信任我！！！',
    ],
    家人: [
      '你们知道我有多累吗！！！我已经在用力活着了！！！',
      '我都这么大了！！！能不能别管这个！！！',
      '你们每次这样说我就崩溃一次你们知道吗！！！',
      '我知道是好意！！！但我真的撑不住这种好意了！！！',
      '爱我就让我喘口气！！！拜托了！！！',
      '我是你们孩子！！！不是你们焦虑的容器！！！',
      '催催催！！！催出一个好的来给你们看看！！！',
      '我在外面已经很辛苦了！！！回家能不能歇一下！！！',
      '我不是不孝！！！我只是需要空间！！！',
      '我的人生能不能让我自己过！！！',
      '我已经焦虑到不行了！！！你们再催要爆了！！！',
      '我想哭！！！但哭了你们更担心！！！两难！！！',
      '我在努力了！！！真的在努力！！！看不见吗！！！',
      '让我先活下去好吗！！！其他以后再说！！！',
      '我很累！！！你们不知道我有多累！！！',
      '我妈说了一句我哭了一路的那种累你们懂吗！！！',
      '我也想给你们好消息！！！但好消息不是催出来的！！！',
      '我每次回家都要被问这些！！！我开始害怕回家了！！！',
      '有没有人问过我还好不好！！！',
      '家是我充电的地方！！！不是来放电的！！！',
    ],
    伴侣: [
      '你根本不懂我！！！我说了多少次了！！！',
      '我的眼泪已经哭干了！！！库存告急！！！',
      '我快被逼疯了！！！真的！！！这次是真的！！！',
      '你知道我为这段关系付出多少吗！！！',
      '我现在的心情你感受不到是吗！！！',
      '我要爆炸了！！！你看得出来吗！！！',
      '好你赢了！！！我不争了！！！我累了！！！',
      '为什么我总是要先道歉！！！',
      '我是不是隐形的！！！你好像根本看不见我！！！',
      '我的委屈放哪里！！！没地方放！！！',
      '爱一个人要这么累吗！！！',
      '我说了我说了我说了！！！你有没有在听！！！',
      '我不是你的出气筒！！！我也有情绪！！！',
      '我需要被照顾！！！不只是照顾你！！！',
      '算了算了！！！（但没有算了，就是还在意）！！！',
      '我在这段关系里消失了！！！',
      '你能不能主动一次！！！就一次！！！',
      '我感觉在一个人谈恋爱！！！',
      '我变得好陌生！！！我都不认识我自己了！！！',
      '你有没有想过我的感受！！！有没有！！！',
    ],
    朋友: [
      '我就是个24小时待机的工具人是吗！！！',
      '我的底线在哪你们真不知道吗！！！',
      '从现在起我要对自己好一点！！！不伺候了！！！',
      '友情的代价是我全部精力吗！！！',
      '我以为你懂我！！！我错了！！！',
      '我也需要被关心！！！有没有人问过我还好吗！！！',
      '我的情绪价值谁来给我！！！',
      '帮到最后帮到自己崩了！！！这合理吗！！！',
      '需要我就出现！！！不需要就消失！！！我不是外卖！！！',
      '我撑不住了！！！我也要人帮我！！！',
      '感情是双向的！！！有没有听说过这个概念！！！',
      '我要休息了！！！社交电量归零！！！',
      '我爱你们但我需要边界！！！',
      '我今天不想做任何人的树洞！！！',
      '为什么总是我在付出！！！',
      '我的耐心是有限的！！！我也会消耗完的！！！',
      '下次你们有事别找我了！！！（但我知道我还是会帮）！！！',
      '我很累！！！不只是身体上的累！！！',
      '我想被人惦记一下！！！就一下！！！',
      '我有事没人找！！！别人有事全来找我！！！',
    ],
    老师: [
      '我的GPA已经是一地碎片了！！！',
      '我真的学不会了！！！我就是块朽木！！！',
      '我看了八遍还是不会！！！救命！！！',
      '我的脑子是空的！！！什么都装不进去！！！',
      '完蛋了！！！这门课我彻底跟不上了！！！',
      '期末了！！！我现在才开始复习！！！太晚了！！！',
      '为什么看懂了还是不会做题！！！原理去哪了！！！',
      '书看了忘！！！忘了看！！！看了还是忘！！！',
      '我不是不努力！！！就是没有天赋！！！',
      '挂科了！！！我的人生！！！',
      '我太难了！！！太难了！！！',
      '考试那天我能消失吗！！！',
      '我学到脑壳疼了！！！还是不会！！！',
      '老师给我一点点机会！！！一点点就够！！！',
      '考试焦虑已经严重影响到我考试了！！！',
      '为什么别人轻松满分我拼死及格！！！',
      '我的努力去哪了！！！被题目吃掉了！！！',
      '我不适合读书！！！但我没有别的选择！！！',
      '救救我！！！我要沉下去了！！！',
      '平时听懂了！！！考试全忘了！！！这是什么诅咒！！！',
    ],
    甲方: [
      '改了多少版了！！！我数不清了！！！',
      '需求说了改！！！改了再改！！！改回第一版！！！',
      '我的创意死在这份改稿单上了！！！',
      '再改一版！！！今天最可怕的话！！！',
      '我的灵感已经和我离婚了！！！',
      '方向改了八百次！！！我找不到北了！！！',
      '明天早上！！！是哪个星球的明天！！！',
      '免费的最贵！！！改到我自费住院！！！',
      '我很专业的！！！求您信任我一次！！！',
      '参考案例发来了！！！抄的话你自己抄！！！',
      '我的方案被否了！！！但他们做的就是我否掉的那版！！！',
      '再这样我要改行了！！！',
      '您说的都对！！！（脑仁已经碎了）！！！',
      '好的没问题！！！（泪水在转圈圈）！！！',
      '方向定了！！！好的！！！又变了！！！',
      '需求文档有吗！！！没有！！！纯靠猜！！！',
      '我快不行了！！！但稿子还没到位！！！',
      '十稿了！！！十稿了！！！',
      '这是我的作品！！！不是橡皮泥！！！',
      '我做梦都在改稿！！！噩梦！！！',
    ],
    室友: [
      '我在自己家里变得如此不自在！！！',
      '我只想安静一下！！！一下下！！！',
      '公区的东西不是我一个人的清洁责任！！！',
      '我晚上要睡觉！！！正常需求！！！',
      '我的耐心到极限了！！！说过一遍了！！！',
      '住在一起不代表我要忍受一切！！！',
      '我的东西为什么要共享！！！没问过我！！！',
      '能不能把垃圾扔掉！！！我要发疯了！！！',
      '我要把门关上！！！永远！！！',
      '合租这件事我后悔了！！！',
      '为什么我说三遍！！！',
      '我今天住图书馆！！！因为那里安静！！！',
      '我想要一个自己的房间！！！哭了！！！',
      '我只是个想好好睡觉的普通人！！！',
      '凌晨两点！！！我还在被折磨！！！',
      '共享厨房！！！变成了对方的专用仓库！！！',
      '我买的东西！！！怎么就没了！！！',
      '我不是在挑剔！！！我只是想正常生活！！！',
      '下次我要一个人住！！！哪怕贵一倍！！！',
      '我忍了三个月了！！！终于说了！！！',
    ],
    相亲: [
      '我不想来！！！但我来了！！！这就是孝顺！！！',
      '我妈觉得合适！！！但我妈不是我！！！',
      '好人！！！好人！！！但不是我的好人！！！',
      '为什么相亲像面试！！！我来的是咖啡馆！！！',
      '彼此彼此！！！互相放过！！！',
      '相了多少次了！！！我快对感情祛魅了！！！',
      '条件很好！！！但我的心动呢！！！心动在哪里！！！',
      '为什么要这么多条件！！！找工作吗！！！',
      '我只是想找个真的喜欢的人！！！有这么难吗！！！',
      '相亲相到怀疑人生了！！！',
      '缘分你在哪！！！我很着急！！！',
      '我妈比我还焦虑！！！我被传染了！！！',
      '又没有！！！下一个！！！',
      '算了！！！爱来不来！！！（但还是想来）！！！',
      '我的择偶标准从100分降到60分了！！！还是没有！！！',
      '每次相亲我都要表演一个完美的自己！！！累死了！！！',
      '对方在看我条件！！！我在看对方眼神！！！都是表演！！！',
      '第几次了！！！我已经不记得了！！！',
      '感情这件事能不能批发！！！',
      '我想恋爱！！！但不想相亲！！！这矛盾吗！！！',
    ],
  }
};

/* ═══════════════════════════════════════════════════════
   STATE
═══════════════════════════════════════════════════════ */
let currentRole = '同事';
let isLoading   = false;
let lastResult  = '';
let lastMode    = 'high';
// Prevent consecutive repeats
const recentResults = { high:{}, crazy:{} };

/* ═══════════════════════════════════════════════════════
   ROLE TABS
═══════════════════════════════════════════════════════ */
function updateIndicator(tab) {
  const ind  = document.getElementById('roleIndicator');
  const wrap = document.getElementById('roleWrap');
  const wr = wrap.getBoundingClientRect();
  const tr = tab.getBoundingClientRect();
  ind.style.left  = (tr.left - wr.left + wrap.scrollLeft) + 'px';
  ind.style.width = tr.width + 'px';
}

document.querySelectorAll('.role-tab').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('.role-tab').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    currentRole = btn.dataset.role;
    updateIndicator(btn);
    resetOutput();
    document.getElementById('inputText').placeholder =
      PLACEHOLDERS[currentRole] || '说说你的真实想法...';
  });
});

window.addEventListener('load', () => {
  const first = document.querySelector('.role-tab.active');
  if (first) updateIndicator(first);
  initCfgUI();
});

/* ═══════════════════════════════════════════════════════
   LOCAL LOGIC
═══════════════════════════════════════════════════════ */
function pickFresh(arr, role, mode) {
  const key = role + ':' + mode;
  const recent = recentResults[mode][key] || [];
  // Filter out recently used, unless pool is tiny
  let pool = arr.filter(x => !recent.includes(x));
  if (pool.length === 0) { pool = arr; recentResults[mode][key] = []; }
  const chosen = pool[Math.floor(Math.random() * pool.length)];
  // Track last 3
  const track = recentResults[mode][key] || [];
  track.push(chosen);
  if (track.length > 3) track.shift();
  recentResults[mode][key] = track;
  return chosen;
}

function scoreKeywords(triggers, input) {
  return triggers.reduce((s, k) => s + (input.includes(k) ? 1 : 0), 0);
}

function getLocalHigh(input) {
  const d = LOCAL.high[currentRole];
  if (!d) return '我需要想一下再回应。';
  // Score all keyword groups
  let best = null, bestScore = 0;
  for (const item of d.kw) {
    const sc = scoreKeywords(item.t, input);
    if (sc > bestScore) { bestScore = sc; best = item; }
  }
  if (best && bestScore > 0) return pickFresh(best.r, currentRole, 'high');
  return pickFresh(d.def, currentRole, 'high');
}

function getLocalCrazy() {
  const pool = LOCAL.crazy[currentRole] || LOCAL.crazy['同事'];
  return pickFresh(pool, currentRole, 'crazy');
}

/* ═══════════════════════════════════════════════════════
   TRANSLATE
═══════════════════════════════════════════════════════ */
async function translate(mode) {
  if (isLoading) return;
  const input = document.getElementById('inputText').value.trim();
  if (!input) {
    const ta = document.getElementById('inputText');
    ta.classList.add('shake');
    ta.focus();
    setTimeout(() => ta.classList.remove('shake'), 450);
    return;
  }

  lastMode = mode;
  isLoading = true;
  showState('loading');

  const useAI = !!cfgKey;
  document.getElementById('loadingText').textContent =
    useAI ? 'AI 翻译中...' : '正在翻译...';

  let text = '';
  let usedAI = false;

  if (useAI) {
    try {
      text = await callAI(input, mode);
      usedAI = true;
    } catch (e) {
      console.warn('AI failed, fallback:', e.message);
      // Show error indicator
      const dot = document.getElementById('statusDot');
      dot.className = 'status-dot dot-error';
      setTimeout(() => {
        dot.className = cfgKey ? 'status-dot dot-ai' : 'status-dot dot-local';
      }, 3000);
    }
  }

  if (!text) {
    await sleep(120 + Math.random() * 100);
    text = mode === 'high' ? getLocalHigh(input) : getLocalCrazy();
  }

  lastResult = text;

  const badge = document.getElementById('modeBadge');
  const modeLabel = mode === 'high' ? '高情商' : '发疯版';
  if (usedAI) {
    badge.innerHTML = `<span class="badge badge-ai">${modeLabel} · AI · ${currentRole}</span>`;
  } else if (mode === 'high') {
    badge.innerHTML = `<span class="badge badge-eq">${modeLabel} · ${currentRole}</span>`;
  } else {
    badge.innerHTML = `<span class="badge badge-crazy">${modeLabel} · ${currentRole}</span>`;
  }

  document.getElementById('resultText').textContent = '';
  showState('result');

  const speed = usedAI ? 4 : 5;
  await typeWriter(document.getElementById('resultText'), text, speed);
  isLoading = false;
}

async function typeWriter(el, text, speed) {
  el.parentElement.classList.add('result-enter');
  for (let i = 0; i <= text.length; i++) {
    el.textContent = text.slice(0, i);
    await sleep(speed + Math.random() * 4);
  }
}

function sleep(ms) { return new Promise(r => setTimeout(r, ms)); }

function showState(s) {
  document.getElementById('statePlaceholder').style.display = s === 'placeholder' ? 'block' : 'none';
  document.getElementById('stateLoading').style.display     = s === 'loading'     ? 'block' : 'none';
  document.getElementById('stateResult').style.display      = s === 'result'      ? 'flex'  : 'none';
}

function resetOutput() { lastResult=''; isLoading=false; showState('placeholder'); }

function resetAll() {
  document.getElementById('inputText').value = '';
  document.getElementById('charCount').textContent = '0 / 300';
  document.getElementById('charCount').style.color = '#ddd';
  resetOutput();
  document.getElementById('inputText').focus();
}

async function copyResult() {
  if (!lastResult) return;
  const btn = document.getElementById('copyBtn');
  await navigator.clipboard.writeText(lastResult);
  btn.textContent = '已复制 ✓';
  btn.style.color = '#000';
  showToast();
  setTimeout(() => {
    btn.innerHTML = '复制 <span style="font-size:12px">↗</span>';
    btn.style.color = '';
  }, 2000);
}

function showToast() {
  const t = document.getElementById('toast');
  t.classList.add('show');
  setTimeout(() => t.classList.remove('show'), 2000);
}

/* ═══════════════════════════════════════════════════════
   EVENTS
═══════════════════════════════════════════════════════ */
document.getElementById('inputText').addEventListener('keydown', e => {
  if (e.key === 'Enter' && !e.shiftKey) { e.preventDefault(); translate('high'); }
});
document.getElementById('inputText').addEventListener('input', function () {
  const len = this.value.length;
  const c = document.getElementById('charCount');
  c.textContent = `${len} / 300`;
  c.style.color = len > 240 ? '#dc2626' : len > 180 ? '#f59e0b' : '#ddd';
});
document.getElementById('btnHigh').addEventListener('click',  () => translate('high'));
document.getElementById('btnCrazy').addEventListener('click', () => translate('crazy'));
</script>
</body>
