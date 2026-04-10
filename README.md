
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>嘴替 · Translator</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    *, *::before, *::after { box-sizing: border-box; }
    :root { --ease-expo: cubic-bezier(0.19, 1, 0.22, 1); --ease-quart: cubic-bezier(0.76, 0, 0.24, 1); }

    body {
      font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display", "Helvetica Neue", Arial, sans-serif;
      -webkit-font-smoothing: antialiased;
      background: #fff;
      color: #000;
    }

    textarea {
      resize: none; border: none; outline: none;
      background: transparent; caret-color: #000;
    }
    textarea::placeholder { color: #d8d8d8; }

    /* ── Role Pills ── */
    .role-pill {
      font-size: 11px; letter-spacing: 0.1em;
      padding: 5px 14px; border: 1px solid #e5e5e5;
      background: transparent; color: #aaa;
      cursor: pointer; font-family: inherit;
      transition: all 0.22s var(--ease-quart);
      white-space: nowrap;
    }
    .role-pill:hover { border-color: #000; color: #000; }
    .role-pill.active { background: #000; border-color: #000; color: #fff; }

    /* ── Buttons ── */
    .btn {
      cursor: pointer; font-family: inherit;
      letter-spacing: 0.12em; text-transform: uppercase;
      transition: transform 0.3s var(--ease-expo), background 0.28s var(--ease-quart), color 0.28s var(--ease-quart), opacity 0.2s;
    }
    .btn:hover  { transform: translateY(-2px); }
    .btn:active { transform: translateY(0); opacity: 0.75; }
    .btn-solid  { background: #000; color: #fff; }
    .btn-solid:hover { background: #222; }
    .btn-outline { background: transparent; color: #000; border: 1px solid #000; }
    .btn-outline:hover { background: #000; color: #fff; }

    /* ── Divider ── */
    .vdivider { width: 1px; background: #f0f0f0; align-self: stretch; margin: 0 52px; flex-shrink: 0; }

    /* ── Animations ── */
    @keyframes fadeUp {
      from { opacity: 0; transform: translateY(10px); }
      to   { opacity: 1; transform: translateY(0); }
    }
    @keyframes blink {
      0%,80%,100% { opacity: 0.15; } 40% { opacity: 1; }
    }
    .fade-up { animation: fadeUp 0.5s var(--ease-expo) forwards; }
    .dot { display: inline-block; animation: blink 1.2s infinite; }
    .dot:nth-child(2) { animation-delay: 0.18s; }
    .dot:nth-child(3) { animation-delay: 0.36s; }

    /* ── Quote card ── */
    .quote-mark::before {
      content: '\201C';
      position: absolute; top: -14px; left: -4px;
      font-size: 64px; line-height: 1; color: #f0f0f0;
      font-family: Georgia, serif; pointer-events: none; user-select: none;
    }

    /* ── Toast ── */
    #toast {
      position: fixed; bottom: 28px; left: 50%;
      transform: translateX(-50%) translateY(50px);
      background: #000; color: #fff;
      font-size: 10px; letter-spacing: 0.15em; text-transform: uppercase;
      padding: 11px 22px;
      transition: transform 0.45s var(--ease-expo), opacity 0.4s ease;
      opacity: 0; pointer-events: none; white-space: nowrap;
    }
    #toast.show { transform: translateX(-50%) translateY(0); opacity: 1; }

    /* ── Badge ── */
    .badge {
      font-size: 9px; letter-spacing: 0.2em; text-transform: uppercase;
      padding: 3px 8px; border: 1px solid; display: inline-block;
    }
    .badge-eq    { border-color: #000; color: #000; }
    .badge-crazy { border-color: #dc2626; color: #dc2626; }

    /* ── Chip counter ── */
    #charCount { transition: color 0.25s ease; }

    /* ── Role chips scrollable on mobile ── */
    .role-scroll { display: flex; gap: 8px; flex-wrap: wrap; }

    @media (max-width: 768px) {
      .main-grid { flex-direction: column !important; }
      .vdivider  { display: none; }
      .right-col { border-top: 1px solid #f0f0f0; padding-top: 36px; margin-top: 36px; }
      .role-scroll { flex-wrap: nowrap; overflow-x: auto; padding-bottom: 4px; }
      .role-scroll::-webkit-scrollbar { display: none; }
    }
  </style>
</head>
<body>

  <!-- ══════════ HEADER ══════════ -->
  <header style="padding: 56px 0 40px; text-align: center;">
    <p style="font-size: 10px; letter-spacing: 0.25em; color: #c8c8c8; text-transform: uppercase; margin: 0 0 10px;">
      Universal Translator
    </p>
    <h1 style="font-size: 26px; font-weight: 600; letter-spacing: -0.03em; margin: 0 0 8px;">
      嘴替
    </h1>
    <p style="font-size: 12px; color: #b8b8b8; font-weight: 300; letter-spacing: 0.05em; margin: 0;">
      想说的话，让我来说。
    </p>
  </header>

  <!-- ══════════ MAIN ══════════ -->
  <main style="max-width: 980px; margin: 0 auto; padding: 0 36px 90px;">
    <div class="main-grid" style="display: flex; align-items: flex-start;">

      <!-- ── LEFT ── -->
      <div style="flex: 1; display: flex; flex-direction: column; min-width: 0;">

        <!-- Label -->
        <p style="font-size: 9px; letter-spacing: 0.22em; color: #d0d0d0; text-transform: uppercase; margin: 0 0 16px;">
          对象 &nbsp;/&nbsp; Who
        </p>

        <!-- Role Pills -->
        <div class="role-scroll" id="roleGroup" style="margin-bottom: 28px;">
          <button class="role-pill active" data-role="同事">同事</button>
          <button class="role-pill" data-role="领导">领导</button>
          <button class="role-pill" data-role="家人">家人</button>
          <button class="role-pill" data-role="朋友">朋友</button>
          <button class="role-pill" data-role="恋人">恋人</button>
          <button class="role-pill" data-role="老师">老师</button>
        </div>

        <!-- Input label -->
        <p style="font-size: 9px; letter-spacing: 0.22em; color: #d0d0d0; text-transform: uppercase; margin: 0 0 16px;">
          想法 &nbsp;/&nbsp; Input
        </p>

        <!-- Textarea -->
        <textarea
          id="inputText"
          rows="6"
          style="font-size: 19px; line-height: 1.65; font-weight: 300; color: #000; width: 100%;"
          placeholder="说说你的真实想法..."
          maxlength="300"
        ></textarea>

        <!-- Underline + char count -->
        <div style="display: flex; justify-content: flex-end; margin-top: 6px;">
          <span id="charCount" style="font-size: 10px; color: #d8d8d8;">0 / 300</span>
        </div>
        <div style="height: 1px; background: #efefef; margin-top: 4px;"></div>

        <!-- Buttons -->
        <div style="display: flex; gap: 10px; margin-top: 24px;">
          <button class="btn btn-solid" id="btnHigh"
            style="flex: 1.2; padding: 15px 10px; font-size: 11px; font-weight: 500;">
            一键高情商
          </button>
          <button class="btn btn-outline" id="btnCrazy"
            style="flex: 1; padding: 15px 10px; font-size: 11px; font-weight: 500;">
            彻底发疯
          </button>
        </div>

        <p style="font-size: 10px; color: #ddd; margin-top: 12px; letter-spacing: 0.03em;">
          Enter 翻译 &nbsp;·&nbsp; Shift+Enter 换行
        </p>

      </div>

      <!-- ── Vertical Divider ── -->
      <div class="vdivider"></div>

      <!-- ── RIGHT ── -->
      <div class="right-col" style="flex: 1; display: flex; flex-direction: column; min-width: 0;">

        <p style="font-size: 9px; letter-spacing: 0.22em; color: #d0d0d0; text-transform: uppercase; margin: 0 0 16px;">
          翻译 &nbsp;/&nbsp; Output
        </p>

        <!-- Placeholder -->
        <div id="statePlaceholder" style="padding-top: 8px;">
          <p style="font-size: 19px; font-weight: 300; color: #e4e4e4; line-height: 1.65; margin: 0;">
            翻译结果在这里出现...
          </p>
        </div>

        <!-- Loading -->
        <div id="stateLoading" style="display: none; padding-top: 10px;">
          <span class="dot" style="font-size: 28px; color: #ccc;">·</span>
          <span class="dot" style="font-size: 28px; color: #ccc;">·</span>
          <span class="dot" style="font-size: 28px; color: #ccc;">·</span>
        </div>

        <!-- Result -->
        <div id="stateResult" style="display: none; flex-direction: column;">
          <div id="modeBadge" style="margin-bottom: 18px;"></div>
          <div class="quote-mark" style="position: relative; padding-left: 4px; min-height: 80px;">
            <p id="resultText"
              style="font-size: 19px; font-weight: 300; line-height: 1.75; margin: 0; color: #000; word-break: break-all;">
            </p>
          </div>
          <!-- Actions -->
          <div style="margin-top: 28px; padding-top: 18px; border-top: 1px solid #f5f5f5; display: flex; gap: 20px;">
            <button onclick="copyResult()"
              style="font-size: 10px; letter-spacing: 0.13em; color: #b8b8b8; text-transform: uppercase;
                     background: none; border: none; cursor: pointer; padding: 0; font-family: inherit;
                     transition: color 0.25s ease;"
              onmouseover="this.style.color='#000'" onmouseout="this.style.color='#b8b8b8'">
              复制 ↗
            </button>
            <button onclick="resetAll()"
              style="font-size: 10px; letter-spacing: 0.13em; color: #b8b8b8; text-transform: uppercase;
                     background: none; border: none; cursor: pointer; padding: 0; font-family: inherit;
                     transition: color 0.25s ease;"
              onmouseover="this.style.color='#000'" onmouseout="this.style.color='#b8b8b8'">
              重新来过
            </button>
          </div>
        </div>

      </div>
    </div><!-- /main-grid -->

    <!-- Footer -->
    <div style="margin-top: 72px; display: flex; align-items: center; gap: 20px;">
      <div style="flex: 1; height: 1px; background: #f5f5f5;"></div>
      <span style="font-size: 9px; letter-spacing: 0.2em; color: #e0e0e0; text-transform: uppercase; white-space: nowrap;">
        嘴替 · 2025
      </span>
      <div style="flex: 1; height: 1px; background: #f5f5f5;"></div>
    </div>
  </main>

  <!-- Toast -->
  <div id="toast">已复制</div>

  <!-- ══════════ SCRIPT ══════════ -->
  <script>

    /* ════════════════════════════════════════
       翻译语料库 — 按角色分类，保持简短自然
       ════════════════════════════════════════ */
    const DATA = {

      high: {
        同事: {
          keywords: [
            { t: ['方案','需求','改','修改','重做'],    r: '改可以，能先对齐一下目标吗？' },
            { t: ['加班','今晚','周末','假期','通宵'],  r: '这个时间我有其他安排，工作日优先跟进。' },
            { t: ['锅','背锅','甩锅','不是我'],         r: '我来梳理一下时间线，咱们对齐一下分工。' },
            { t: ['催','什么时候','ddl','deadline'],    r: '在推了，有进展第一时间同步你。' },
            { t: ['你来','你负责','丢给我','帮我做'],   r: '这块超出我的范围了，要不拉上leader确认？' },
            { t: ['不干','不做','不想','拒绝'],         r: '这个我目前承接不了，我们看看怎么重新分工。' },
          ],
          def: [
            '我这边确认下，回头同步你。',
            '这个点我有不同看法，聊一下？',
            '我理解，但这样推有点风险。',
            '排期有点紧，能往后排吗？',
          ]
        },
        领导: {
          keywords: [
            { t: ['方案','改','修改','重做','不好'],    r: '收到，有方向参考吗，这样调整更准。' },
            { t: ['加班','周末','今晚','赶一下'],       r: '好的，我尽力，有卡点提前报备您。' },
            { t: ['锅','责任','谁的问题'],              r: '我来整理过程记录，方便明确后续分工。' },
            { t: ['催','进度','怎么还没','什么时候'],   r: '收到，马上同步最新进展给您。' },
            { t: ['完不成','做不到','太多','不合理'],   r: '想跟您说下目前的工作量，看看优先级怎么排。' },
          ],
          def: [
            '收到，尽快落实。',
            '明白，我确认下细节再推进。',
            '好的，有情况第一时间汇报。',
            '我需要确认一个前提，麻烦给个方向。',
          ]
        },
        家人: {
          keywords: [
            { t: ['催婚','对象','结婚','找对象'],       r: '这件事我有自己的节奏，到时候会告诉你们的。' },
            { t: ['钱','借钱','给钱','转我'],           r: '我手头也有点紧，这次帮不上。' },
            { t: ['回家','不回来','假期'],              r: '最近事多，我会安排时间的。' },
            { t: ['成绩','工资','赚多少','工作'],       r: '我在努力，给我点时间好吗。' },
            { t: ['不听话','叛逆','你怎么这样'],        r: '我知道你们是好意，但这件事我需要自己决定。' },
          ],
          def: [
            '我知道你是为我好，但让我自己来。',
            '我们能好好说话吗，别这样。',
            '听到了，给我点时间想想。',
            '这件事我有自己的想法，信任我一下。',
          ]
        },
        朋友: {
          keywords: [
            { t: ['借钱','借我','周转','还没还'],       r: '最近我也比较紧，这次帮不上。' },
            { t: ['带货','买','拼单','帮个忙'],         r: '这个不太适合我，下次吧。' },
            { t: ['失约','放鸽子','没来','不去'],       r: '今天状态不行，改天补上，不好意思。' },
            { t: ['说出去','告诉别人','秘密'],          r: '那件事能帮我保密吗，我不想让别人知道。' },
            { t: ['不帮','不理','生气','误会'],         r: '我没有你想的那个意思，我们聊一下？' },
          ],
          def: [
            '我理解你，但这次真的帮不上。',
            '说真的，我需要你别这样。',
            '我最近状态不太好，给我点空间。',
            '你说的有道理，但我有点不同想法。',
          ]
        },
        恋人: {
          keywords: [
            { t: ['分手','不想了','算了','不在一起'],   r: '我现在情绪不稳，我们冷静一下再聊好吗。' },
            { t: ['不接','没回','已读','不理我'],       r: '我刚才需要独处一下，不是故意不理你。' },
            { t: ['生气','怎么了','你干嘛'],            r: '我不是生你气，今天很累，让我缓缓。' },
            { t: ['吃醋','嫉妒','说清楚','那个人'],    r: '我需要你解释一下，我有点不安全感。' },
            { t: ['不关心','不在乎','不爱了'],          r: '我很在乎你，只是不太会表达。' },
          ],
          def: [
            '我不是不在乎你，只是现在说不好。',
            '我们能不吵架，好好说吗。',
            '给我点时间，整理好了来找你。',
            '我珍视我们，所以才会在意这些。',
          ]
        },
        老师: {
          keywords: [
            { t: ['没交','没写','作业','没做完'],       r: '老师对不起，这次没按时完成，我尽快补上。' },
            { t: ['成绩','分数','挂科','没及格'],       r: '老师我想了解这次成绩，能给我一些建议吗？' },
            { t: ['没来','旷课','逃课','点名'],         r: '老师不好意思，那次有特殊情况，已经补了笔记。' },
            { t: ['听不懂','不会','没学会','不明白'],   r: '这里我理解得不够，请问方便再解释一下吗？' },
          ],
          def: [
            '谢谢老师，我记下了，会认真改进的。',
            '老师说得对，是我没做好。',
            '我回去再想想，有问题再来请教。',
            '我理解老师的意思，我来调整一下。',
          ]
        },
      },

      crazy: {
        同事: [
          '我已经不是人了！！！我是台不用充电的机器！！！',
          '什么叫一个人干三个人的活！！！这就叫！！！',
          '我的精神状态是薛定谔的崩溃！！！随时塌方！！！',
          '我理智已经离职了！！！没有工作交接直接跑路！！！',
          '好的没问题！！！（内心已经原地爆炸三次了）！！！',
        ],
        领导: [
          '好的好的！！！反正说不行也没用！！！',
          '收到！！！（内心：我要原地爆炸了）！！！',
          '明白！！！（但我完全不明白凭什么是我）！！！',
          '没问题！！！（所有问题都压在我身上了）！！！',
          '行！！！我就当自己是牛！！！使劲薅！！！',
        ],
        家人: [
          '你们知道我有多累吗！！！我已经尽力了！！！',
          '我都这么大了！！！能不能别管这个！！！',
          '你们每次这样说我就崩溃你们知道吗！！！',
          '我知道是好意但我真的受不了了！！！',
          '爱我就让我喘口气！！！拜托了！！！',
        ],
        朋友: [
          '我就是个24小时随叫随到的工具人是吗！！！',
          '我的底线在哪你们真的不知道吗！！！',
          '我以为你懂我！！！我错了！！！',
          '从现在起我要对自己好一点！！！不伺候了！！！',
          '友情的代价是我的全部精力是吗！！！！',
        ],
        恋人: [
          '你根本不懂我！！！我说了多少次了！！！',
          '我现在的心情你完全感受不到是吗！！！',
          '我快被逼疯了！！！真的！！！',
          '你知道我为这段关系付出多少吗！！！',
          '我的眼泪已经哭干了！！！库存告急！！！',
        ],
        老师: [
          '我真的学不会了！！！我就是一块朽木！！！',
          '完了完了！！！这门课我彻底跟不上了！！！',
          '我的脑子是空的！！！什么都装不进去！！！',
          '救命啊！！！我看了八遍还是不会！！！',
          '我的GPA已经是一地碎片了！！！！',
        ],
      }
    };

    /* ════════ State ════════ */
    let currentRole = '同事';
    let isLoading   = false;
    let lastResult  = '';

    /* ════════ Role Pills ════════ */
    document.querySelectorAll('.role-pill').forEach(btn => {
      btn.addEventListener('click', () => {
        document.querySelectorAll('.role-pill').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        currentRole = btn.dataset.role;
        // 切换角色时更新占位符
        resetOutput();
      });
    });

    /* ════════ Logic ════════ */
    function getHighText(input) {
      const roleData = DATA.high[currentRole];
      if (!roleData) return '我需要一点时间来想清楚再回应。';

      for (const item of roleData.keywords) {
        if (item.t.some(k => input.includes(k))) return item.r;
      }
      const d = roleData.def;
      return d[Math.floor(Math.random() * d.length)];
    }

    function getCrazyText() {
      const pool = DATA.crazy[currentRole] || DATA.crazy['同事'];
      return pool[Math.floor(Math.random() * pool.length)];
    }

    async function translate(mode) {
      if (isLoading) return;

      const input = document.getElementById('inputText').value.trim();
      if (!input) {
        const ta = document.getElementById('inputText');
        const ori = ta.placeholder;
        ta.placeholder = '先写点什么吧 ↑';
        ta.focus();
        setTimeout(() => { ta.placeholder = ori; }, 1800);
        return;
      }

      isLoading = true;
      showState('loading');

      // ── 快速响应：150–300ms ──
      await sleep(150 + Math.random() * 150);

      const text = mode === 'high' ? getHighText(input) : getCrazyText();
      lastResult = text;

      // Badge
      const badge = document.getElementById('modeBadge');
      badge.innerHTML = mode === 'high'
        ? `<span class="badge badge-eq">高情商 · ${currentRole}</span>`
        : `<span class="badge badge-crazy">发疯版 · ${currentRole}</span>`;

      document.getElementById('resultText').textContent = '';
      showState('result');

      // 打字机：速度快
      await typeWriter(document.getElementById('resultText'), text, 6);
      isLoading = false;
    }

    async function typeWriter(el, text, speed) {
      el.classList.add('fade-up');
      for (let i = 0; i <= text.length; i++) {
        el.textContent = text.slice(0, i);
        await sleep(speed + Math.random() * 6);
      }
    }

    function sleep(ms) { return new Promise(r => setTimeout(r, ms)); }

    function showState(s) {
      document.getElementById('statePlaceholder').style.display = s === 'placeholder' ? 'block' : 'none';
      document.getElementById('stateLoading').style.display     = s === 'loading'     ? 'block' : 'none';
      document.getElementById('stateResult').style.display      = s === 'result'      ? 'flex'  : 'none';
    }

    function resetOutput() {
      lastResult = '';
      isLoading  = false;
      showState('placeholder');
    }

    function resetAll() {
      document.getElementById('inputText').value = '';
      document.getElementById('charCount').textContent = '0 / 300';
      document.getElementById('charCount').style.color = '#d8d8d8';
      resetOutput();
      document.getElementById('inputText').focus();
    }

    function copyResult() {
      if (!lastResult) return;
      navigator.clipboard.writeText(lastResult).then(showToast);
    }

    function showToast() {
      const t = document.getElementById('toast');
      t.classList.add('show');
      setTimeout(() => t.classList.remove('show'), 2000);
    }

    /* ════════ Events ════════ */
    document.getElementById('inputText').addEventListener('keydown', e => {
      if (e.key === 'Enter' && !e.shiftKey) { e.preventDefault(); translate('high'); }
    });

    document.getElementById('inputText').addEventListener('input', function () {
      const len = this.value.length;
      const c   = document.getElementById('charCount');
      c.textContent = `${len} / 300`;
      c.style.color = len > 240 ? '#dc2626' : len > 180 ? '#f59e0b' : '#d8d8d8';
    });

    document.getElementById('btnHigh').addEventListener('click',  () => translate('high'));
    document.getElementById('btnCrazy').addEventListener('click', () => translate('crazy'));

  </script>
</body>
</html>
