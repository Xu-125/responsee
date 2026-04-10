 <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
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
      background: #fff; color: #000;
      overflow-x: hidden;
    }

    /* ── Page entrance ── */
    @keyframes pageIn {
      from { opacity: 0; transform: translateY(16px); }
      to   { opacity: 1; transform: translateY(0); }
    }
    .page-enter { animation: pageIn 0.7s var(--ease-expo) both; }
    .delay-1 { animation-delay: 0.05s; }
    .delay-2 { animation-delay: 0.12s; }
    .delay-3 { animation-delay: 0.2s; }
    .delay-4 { animation-delay: 0.28s; }
    .delay-5 { animation-delay: 0.36s; }

    /* ── Role tabs ── */
    .role-wrap {
      position: relative;
      display: flex;
      gap: 0;
      border-bottom: 1px solid #f0f0f0;
      overflow-x: auto;
      scrollbar-width: none;
    }
    .role-wrap::-webkit-scrollbar { display: none; }

    .role-tab {
      position: relative; z-index: 1;
      padding: 8px 16px;
      font-size: 12px; letter-spacing: 0.08em;
      color: #c0c0c0; background: none; border: none;
      cursor: pointer; font-family: inherit;
      white-space: nowrap;
      transition: color 0.25s var(--ease-quart);
      flex-shrink: 0;
    }
    .role-tab:hover { color: #666; }
    .role-tab.active { color: #000; font-weight: 500; }

    /* Sliding underline indicator */
    .role-indicator {
      position: absolute;
      bottom: -1px; height: 2px;
      background: #000;
      transition: left 0.38s var(--ease-quart), width 0.38s var(--ease-quart);
      pointer-events: none;
    }

    /* ── Textarea ── */
    textarea {
      resize: none; border: none; outline: none;
      background: transparent; caret-color: #000;
      transition: opacity 0.2s;
    }
    textarea::placeholder { color: #ddd; }
    .input-line {
      height: 1px; background: #efefef;
      transform-origin: left;
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
                  box-shadow 0.3s ease,
                  opacity 0.2s;
      position: relative; overflow: hidden;
    }
    .btn::after {
      content: '';
      position: absolute; inset: 0;
      background: rgba(255,255,255,0.12);
      opacity: 0;
      transition: opacity 0.25s;
    }
    .btn:hover { transform: translateY(-2px); }
    .btn:hover::after { opacity: 1; }
    .btn:active { transform: translateY(0) scale(0.98); opacity: 0.8; }

    .btn-solid { background: #000; color: #fff; }
    .btn-solid:hover { box-shadow: 0 6px 20px rgba(0,0,0,0.18); }
    .btn-outline { background: transparent; color: #000; border: 1px solid #ccc; }
    .btn-outline:hover { background: #000; color: #fff; border-color: #000;
                         box-shadow: 0 6px 20px rgba(0,0,0,0.15); }

    /* ── Divider ── */
    .vdivider { width: 1px; background: #f0f0f0; align-self: stretch; margin: 0 52px; flex-shrink: 0; }

    /* ── Loading bars ── */
    .bar-wrap { display: flex; align-items: flex-end; gap: 4px; height: 24px; }
    @keyframes barBounce {
      0%,100% { transform: scaleY(0.25); opacity: 0.25; }
      50%      { transform: scaleY(1);    opacity: 1; }
    }
    .bar {
      width: 3px; height: 100%; background: #ccc;
      transform-origin: bottom;
      animation: barBounce 0.9s ease-in-out infinite;
    }
    .bar:nth-child(2) { animation-delay: 0.15s; }
    .bar:nth-child(3) { animation-delay: 0.3s; }
    .bar:nth-child(4) { animation-delay: 0.45s; }
    .bar:nth-child(5) { animation-delay: 0.6s; }

    /* ── Result entrance ── */
    @keyframes resultIn {
      from { opacity: 0; transform: translateY(14px); }
      to   { opacity: 1; transform: translateY(0); }
    }
    .result-enter { animation: resultIn 0.55s var(--ease-expo) both; }

    /* ── Quote mark ── */
    .quote-mark { position: relative; padding-left: 4px; }
    .quote-mark::before {
      content: '\201C';
      position: absolute; top: -10px; left: -2px;
      font-size: 56px; line-height: 1; color: #f0f0f0;
      font-family: Georgia, serif;
      pointer-events: none; user-select: none;
      transition: color 0.4s;
    }

    /* ── Badge ── */
    .badge {
      font-size: 9px; letter-spacing: 0.2em; text-transform: uppercase;
      padding: 3px 9px; border: 1px solid; display: inline-flex;
      align-items: center; gap: 6px;
    }
    .badge::before {
      content: ''; width: 5px; height: 5px;
      border-radius: 50%; flex-shrink: 0;
    }
    .badge-eq    { border-color: #000; color: #000; }
    .badge-eq::before { background: #000; }
    .badge-crazy { border-color: #dc2626; color: #dc2626; }
    .badge-crazy::before { background: #dc2626; animation: barBounce 0.7s infinite; }

    /* ── Action links ── */
    .action-link {
      font-size: 10px; letter-spacing: 0.13em; text-transform: uppercase;
      background: none; border: none; cursor: pointer;
      padding: 0; font-family: inherit;
      color: #c8c8c8;
      transition: color 0.22s ease, transform 0.25s var(--ease-back);
      display: inline-flex; align-items: center; gap: 4px;
    }
    .action-link:hover { color: #000; transform: translateY(-1px); }

    /* ── Toast ── */
    #toast {
      position: fixed; bottom: 28px; left: 50%;
      transform: translateX(-50%) translateY(50px);
      background: #000; color: #fff;
      font-size: 10px; letter-spacing: 0.15em; text-transform: uppercase;
      padding: 11px 22px;
      transition: transform 0.45s var(--ease-expo), opacity 0.4s ease;
      opacity: 0; pointer-events: none; white-space: nowrap;
      display: flex; align-items: center; gap: 8px;
    }
    #toast::before { content: '✓'; font-size: 11px; }
    #toast.show { transform: translateX(-50%) translateY(0); opacity: 1; }

    /* ── Char counter ── */
    #charCount { transition: color 0.25s ease; }

    /* ── Mobile ── */
    @media (max-width: 768px) {
      .main-grid { flex-direction: column !important; }
      .vdivider  { display: none; }
      .right-col { border-top: 1px solid #f0f0f0; padding-top: 36px; margin-top: 36px; }
    }
  </style>
</head>
<body>

  <!-- ══════════ HEADER ══════════ -->
  <header style="padding: 60px 0 44px; text-align: center;">
    <p class="page-enter delay-1"
       style="font-size: 10px; letter-spacing: 0.28em; color: #c8c8c8; text-transform: uppercase; margin: 0 0 10px;">
      Universal Translator
    </p>
    <h1 class="page-enter delay-2"
        style="font-size: 28px; font-weight: 600; letter-spacing: -0.035em; margin: 0 0 8px;">
      嘴替
    </h1>
    <p class="page-enter delay-3"
       style="font-size: 12px; color: #c0c0c0; font-weight: 300; letter-spacing: 0.06em; margin: 0;">
      想说的话，让我来说。
    </p>
  </header>

  <!-- ══════════ MAIN ══════════ -->
  <main style="max-width: 1000px; margin: 0 auto; padding: 0 36px 90px;">
    <div class="main-grid page-enter delay-4" style="display: flex; align-items: flex-start;">

      <!-- ── LEFT ── -->
      <div style="flex: 1; display: flex; flex-direction: column; min-width: 0;">

        <!-- Role label -->
        <p style="font-size: 9px; letter-spacing: 0.22em; color: #d0d0d0; text-transform: uppercase; margin: 0 0 14px;">
          对象 &nbsp;/&nbsp; Who
        </p>

        <!-- Role tabs -->
        <div class="role-wrap" id="roleWrap" style="margin-bottom: 32px;">
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

        <!-- Input label -->
        <p style="font-size: 9px; letter-spacing: 0.22em; color: #d0d0d0; text-transform: uppercase; margin: 0 0 14px;">
          想法 &nbsp;/&nbsp; Input
        </p>

        <!-- Textarea -->
        <div class="input-wrap">
          <textarea
            id="inputText" rows="6"
            style="font-size: 19px; line-height: 1.7; font-weight: 300; color: #000; width: 100%;"
            placeholder="说说你的真实想法..."
            maxlength="300"
          ></textarea>
          <div style="display: flex; justify-content: flex-end; margin-top: 6px;">
            <span id="charCount" style="font-size: 10px; color: #ddd;">0 / 300</span>
          </div>
          <div class="input-line" style="margin-top: 4px;"></div>
        </div>

        <!-- Buttons -->
        <div style="display: flex; gap: 10px; margin-top: 22px;">
          <button class="btn btn-solid" id="btnHigh"
            style="flex: 1.2; padding: 15px 10px; font-size: 11px; font-weight: 500;">
            一键高情商
          </button>
          <button class="btn btn-outline" id="btnCrazy"
            style="flex: 1; padding: 15px 10px; font-size: 11px; font-weight: 500;">
            彻底发疯
          </button>
        </div>

        <p style="font-size: 10px; color: #e0e0e0; margin-top: 12px; letter-spacing: 0.03em;">
          Enter 翻译 &nbsp;·&nbsp; Shift+Enter 换行
        </p>
      </div>

      <!-- ── Divider ── -->
      <div class="vdivider"></div>

      <!-- ── RIGHT ── -->
      <div class="right-col" style="flex: 1; display: flex; flex-direction: column; min-width: 0;">
        <p style="font-size: 9px; letter-spacing: 0.22em; color: #d0d0d0; text-transform: uppercase; margin: 0 0 14px;">
          翻译 &nbsp;/&nbsp; Output
        </p>

        <!-- Placeholder -->
        <div id="statePlaceholder">
          <p style="font-size: 19px; font-weight: 300; color: #e8e8e8; line-height: 1.7; margin: 0;">
            翻译结果在这里出现...
          </p>
        </div>

        <!-- Loading -->
        <div id="stateLoading" style="display:none; padding-top: 8px;">
          <div class="bar-wrap">
            <div class="bar"></div>
            <div class="bar"></div>
            <div class="bar"></div>
            <div class="bar"></div>
            <div class="bar"></div>
          </div>
          <p style="font-size: 11px; color: #d8d8d8; margin: 10px 0 0; letter-spacing: 0.06em;">
            正在翻译中...
          </p>
        </div>

        <!-- Result -->
        <div id="stateResult" style="display:none; flex-direction:column;">
          <div id="modeBadge" style="margin-bottom: 20px;"></div>
          <div class="quote-mark" style="min-height: 80px;">
            <p id="resultText"
               style="font-size: 19px; font-weight: 300; line-height: 1.8; margin: 0; color: #000; word-break: break-all;">
            </p>
          </div>
          <!-- Actions -->
          <div style="margin-top: 28px; padding-top: 18px; border-top: 1px solid #f5f5f5;
                      display: flex; gap: 20px; align-items: center;">
            <button class="action-link" id="copyBtn" onclick="copyResult()">
              复制 <span style="font-size: 12px;">↗</span>
            </button>
            <button class="action-link" onclick="resetAll()">
              重新来过
            </button>
            <button class="action-link" onclick="translate(lastMode)" style="margin-left: auto;">
              换一条 ↺
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Footer -->
    <div style="margin-top: 80px; display: flex; align-items: center; gap: 20px;">
      <div style="flex: 1; height: 1px; background: #f5f5f5;"></div>
      <span style="font-size: 9px; letter-spacing: 0.18em; color: #e2e2e2; white-space: nowrap;">
        @zeyi
      </span>
      <div style="flex: 1; height: 1px; background: #f5f5f5;"></div>
    </div>
  </main>

  <div id="toast">已复制到剪贴板</div>

  <!-- ══════════ SCRIPT ══════════ -->
  <script>

  /* ═══════════════════════════════════════════
     语料库 — 每个 keywords 条目 r 为数组随机取
     ═══════════════════════════════════════════ */
  const DATA = {
    high: {
      同事: {
        keywords: [
          { t:['改','修改','重做','方案','需求','推翻'], r:[
            '改没问题，先对齐一下目标？',
            '可以改，这次的标准是什么？',
            '没问题，改之前先确认需求？',
            '行，改完还会再改吗，先定版本。',
            '这块我有疑问，改之前聊一下？',
          ]},
          { t:['加班','周末','今晚','通宵','假期','节假日'], r:[
            '今晚有安排，明早给你。',
            '这个时间我走不开，能改工作日吗？',
            '周末不太方便，周一第一件事跟进。',
            '我看一下能不能排开，先给你个时间。',
            '可以加，但后面能不能补个调休？',
          ]},
          { t:['催','什么时候','进度','ddl','deadline','提交'], r:[
            '在推了，有进展马上同步你。',
            '今天下班前给你，稍等。',
            '卡了一个问题，解决了立刻发。',
            '80%了，快了。',
            '预计下午三点，我到时候提醒你。',
          ]},
          { t:['锅','背锅','甩锅','不是我','责任','怪我'], r:[
            '我来梳理一下时间线，咱们对齐分工。',
            '我整理一份记录，一起看一下。',
            '这块我们再确认一下，谁负责哪段。',
            '复盘一下，看看哪里可以优化。',
            '说清楚总比说不清楚好，咱们对一下。',
          ]},
          { t:['你来','你做','帮我做','你负责','交给你'], r:[
            '这块不在我范围里，要不拉上leader确认？',
            '我手里事情比较满，排期确认一下再说。',
            '我可以协助，主责得确认一下是谁。',
            '这个需要正式走流程吗？',
            '我来做可以，但需求得给全。',
          ]},
          { t:['不干','不做','不想','拒绝','受不了'], r:[
            '这个我现在承接不了，能重新分工吗？',
            '我手头已经满了，说实话做不过来。',
            '这块我有些顾虑，能聊一下吗？',
            '这个任务我不适合接，建议找更合适的人。',
            '现在优先级这么排，做不到。',
          ]},
          { t:['会议','开会','拉个会','讨论'], r:[
            '能发个议题吗，提前准备一下。',
            '哪些人必须到场？我看一下时间。',
            '这个邮件沟通能解决吗，不然我排一下。',
            '会议多长时间，我这边调一下日历。',
          ]},
          { t:['不公平','凭什么','为什么是我','又是我'], r:[
            '我理解，但这件事我们能说清楚逻辑吗？',
            '我想了解一下这次分工的原则是什么。',
            '能说一下为什么选我负责这块吗？',
            '我有点疑问，找个时间聊聊？',
          ]},
        ],
        def:[
          '我确认一下，回头同步你。',
          '这个点我有不同看法，能聊一下吗？',
          '我理解，但这样推有点风险。',
          '排期有点紧，能往后调吗？',
          '我需要一些上下文，你能补充一下吗？',
          '这个先记下来，下周同步一下。',
          '我看一下，有情况马上找你。',
          '这块我不太确定，先确认再推。',
          '能给我发一下相关文件吗？',
          '好，我来跟进，有问题找你。',
        ]
      },

      领导: {
        keywords: [
          { t:['改','方案','重做','推翻','重来'], r:[
            '收到，能给个方向参考吗？',
            '好的，您理想的效果是？',
            '明白，我重新梳理一版给您看。',
            '收到，改之前能再对齐一下吗？',
            '好，这次的重点是哪几块？',
          ]},
          { t:['加班','周末','赶','今晚','紧急','明天早上'], r:[
            '好的，我尽力，有卡点提前报备。',
            '收到，今晚完成，明早发您。',
            '没问题，有问题随时联系我。',
            '我来安排，时间节点是？',
            '收到，优先处理这个。',
          ]},
          { t:['催','进度','怎么还没','什么时候','结果呢'], r:[
            '收到，马上同步最新进展给您。',
            '正在推，今天下班前给您一版。',
            '这块卡了个问题，我来解决，稍等。',
            '给我两小时，给您一个完整版。',
            '马上，五分钟内发您。',
          ]},
          { t:['不合理','太多','做不完','超量','压力大'], r:[
            '想跟您沟通一下工作量，看优先级怎么排。',
            '我把目前的任务列表发给您看一下？',
            '我需要您帮我确认一下，哪个最优先？',
            '资源上能支持一下吗，我怕质量出问题。',
          ]},
          { t:['绩效','考核','kpi','评级'], r:[
            '我想了解一下对我这块的期待和标准。',
            '能给我一些改进方向的反馈吗？',
            '我这边做了些总结，方便的时候汇报一下？',
          ]},
          { t:['晋升','涨薪','升职','调薪'], r:[
            '我想找个时间跟您聊聊我的发展规划。',
            '我整理了一份工作成果，方便的时候汇报？',
            '想听听您对我下一步发展的建议。',
          ]},
        ],
        def:[
          '收到，尽快落实。',
          '明白，我确认细节再推进。',
          '好的，有情况第一时间汇报。',
          '需要确认一个前提，麻烦给个方向。',
          '我来安排，您放心。',
          '明白了，我来处理。',
          '收到，我跟一下，有结果马上报。',
          '好的，今天内给您一个反馈。',
          '了解，按这个方向推进。',
          '收到，我对齐一下相关方。',
        ]
      },

      家人: {
        keywords: [
          { t:['催婚','结婚','对象','找对象','成家'], r:[
            '这件事我有自己的节奏，急不来。',
            '我知道你们担心，但这个我来。',
            '缘分的事急不了，到时候告诉你们。',
            '我在认真对待这件事，信任我。',
            '你们着急我理解，但我不着急。',
          ]},
          { t:['催生','生孩子','二胎','生娃','要孩子'], r:[
            '这个我们自己决定，到时候告诉你们。',
            '我们有自己的计划，不急。',
            '这件事我们考虑的因素很多，慢慢来。',
          ]},
          { t:['钱','借钱','给钱','转我','打款'], r:[
            '我手头也不宽裕，这次帮不上。',
            '最近比较紧，下次吧。',
            '这个我真的给不了，不好意思。',
            '我自己也有压力，实在难。',
          ]},
          { t:['回家','不回来','假期','怎么不回'], r:[
            '最近太忙了，有空一定回。',
            '我安排一下，尽量回。',
            '我想回，但真的走不开，下次补。',
            '我看看时间，确定了告诉你们。',
          ]},
          { t:['工资','赚多少','多少钱','工作怎么样'], r:[
            '还好，够花，别担心我。',
            '正在努力，给我点时间。',
            '收入还行，你们不用操心。',
            '我能养活自己，放心。',
          ]},
          { t:['管','干涉','你怎么','不听话','不孝'], r:[
            '我知道你们是好意，但这件事让我来。',
            '我们能好好聊吗，别这样说。',
            '我不是不听，我有自己的考虑。',
            '我是你们孩子，但我有自己的判断。',
          ]},
          { t:['吃','睡','身体','累','休息'], r:[
            '我会注意的，不用担心。',
            '我照顾好自己，你们放心。',
            '最近是累，但能撑住，不用担心。',
          ]},
        ],
        def:[
          '我知道你们是为我好。',
          '给我点时间，我会的。',
          '这件事我有自己的想法，信任我。',
          '我们能好好说吗，别这么问。',
          '我很好，真的，不用担心。',
          '让我自己来，我已经长大了。',
          '我听到了，但我需要自己决定。',
          '我爱你们，但这个得我自己扛。',
          '我知道，我在努力。',
          '别担心我，我心里有数。',
        ]
      },

      伴侣: {
        keywords: [
          { t:['分手','不想了','算了','结束','不在一起'], r:[
            '我现在情绪不稳，冷静一下再聊好吗。',
            '我不想用这个词，我们谈谈发生了什么。',
            '能给我一点时间吗，这件事我没想好。',
            '我很在乎这段关系，所以更需要好好说。',
            '先冷静，这么大的事不能冲动决定。',
          ]},
          { t:['不回','没回','已读','不接','不理我'], r:[
            '我刚才需要一点空间，不是不理你。',
            '我在的，刚才状态不好，现在好多了。',
            '不是故意不回，当时走不开。',
            '我没有消失，只是需要缓一缓。',
          ]},
          { t:['生气','怎么了','你干嘛','什么态度'], r:[
            '我不是在生你气，今天很累，让我缓缓。',
            '我有点情绪，不是冲你的。',
            '我需要一点时间平复，不是针对你。',
            '我状态不太好，给我一会儿。',
            '我只是累了，不是因为你。',
          ]},
          { t:['吃醋','嫉妒','那个人','说清楚','解释'], r:[
            '我需要你解释一下，我有点不安全感。',
            '我不是不信任你，我只是需要安慰。',
            '你说一下发生了什么，我听。',
            '我的感受能说说吗，我有点在意。',
          ]},
          { t:['不关心','不在乎','不爱了','心不在','敷衍'], r:[
            '我很在乎你，只是不太会表达。',
            '不是不关心，是我最近状态不好。',
            '我需要你告诉我你想要什么，我来改。',
            '我爱你，但我也需要空间，这两者不矛盾。',
          ]},
          { t:['冷战','不说话','沉默','不理我'], r:[
            '我不想冷战，我们说出来吧。',
            '冷战解决不了问题，我们谈谈？',
            '我想跟你说话，你能回应我吗？',
          ]},
          { t:['你的错','都怪你','是你','你的问题'], r:[
            '我来听听你说，我哪里做得不好。',
            '可以说具体是什么让你不开心吗？',
            '我想理解你的感受，你说。',
            '好，我听，告诉我你需要什么。',
          ]},
          { t:['太忙','没时间','工作','不陪我'], r:[
            '我知道你委屈，我来想办法多留时间给你。',
            '不是不想陪你，我们排个时间专门在一起？',
            '你说得对，我最近忽略你了，对不起。',
          ]},
        ],
        def:[
          '我不是不在乎你，只是现在说不好。',
          '我们能不吵架，好好说吗。',
          '给我一点时间，整理好了来找你。',
          '我珍视我们，所以才会在意这些。',
          '你对我很重要，这点没变过。',
          '我们能说说发生什么了吗？',
          '我听你说，我在。',
          '我需要你，但也需要你理解我。',
          '我们好好聊一下吧，我不想这样。',
          '今晚能谈谈吗，我有些话想说。',
        ]
      },

      朋友: {
        keywords: [
          { t:['借钱','借我','周转','还没还','欠我'], r:[
            '最近我也比较紧，这次帮不上。',
            '说实话我手头不宽裕，不好意思。',
            '钱的事不好帮，我怕影响我们感情。',
            '这次真没法，不是不想帮你。',
          ]},
          { t:['放鸽子','没来','失约','临时取消','不来了'], r:[
            '今天真的状态不行，改天我请你。',
            '不好意思，有急事，我来补。',
            '对不起，下次你定时间，我一定到。',
            '我错了，怎么补偿你？',
          ]},
          { t:['告诉别人','说出去','保密','秘密'], r:[
            '那件事能帮我保密吗，很重要。',
            '能就我们知道吗，我不想扩散。',
            '麻烦别提这件事，谢谢你。',
          ]},
          { t:['帮忙','带货','拼单','买','推荐'], r:[
            '这个不太适合我，下次吧。',
            '我最近在控制消费，过了。',
            '不是不给面子，真不需要。',
          ]},
          { t:['不理','生气','误会','闹矛盾','你怎么了'], r:[
            '我没有你想的那个意思，我们聊一下？',
            '我最近状态不好，不是针对你。',
            '有误会的话说出来，别憋着。',
            '我很重视我们，别让这件事成心结。',
          ]},
          { t:['只找我','索取','利用','工具人'], r:[
            '我需要告诉你，我有点力不从心了。',
            '我们能说说吗，我有些想法想分享。',
            '朋友是双向的，我也需要被照顾。',
          ]},
          { t:['发疯','崩溃','受不了','太难了'], r:[
            '说吧，我听着，不评判。',
            '今天先发泄，明天再想办法。',
            '你不是一个人，我在。',
            '需要我做什么告诉我，我来。',
          ]},
        ],
        def:[
          '我理解你，但这次真的帮不上。',
          '我最近状态不好，给我点空间。',
          '你说得有道理，但我有点不同想法。',
          '我在，你说吧。',
          '这事我们晚点聊，现在不是时候。',
          '我支持你，但你确定要这样做吗？',
          '给我一点时间想想怎么帮你。',
          '我们是好朋友，说实话也没关系。',
          '你不用一个人扛，有我。',
          '放心，我不会评判你的。',
        ]
      },

      老师: {
        keywords: [
          { t:['没交','没写','作业','没做完','忘交'], r:[
            '老师对不起，这次没按时完成，我补上。',
            '不好意思，我今天交可以吗？',
            '我失误了，能给我补交机会吗？',
            '我知道我不对，能说一下截止时间吗？',
          ]},
          { t:['成绩','分数','挂科','没及格','考砸了'], r:[
            '老师我想了解一下哪里扣分了，能看看吗？',
            '这次考得不理想，您有什么建议吗？',
            '我想改进，能给我一些方向吗？',
            '有补考机会吗，我想再试一次。',
          ]},
          { t:['旷课','没来','逃课','点名','缺席'], r:[
            '老师不好意思，那次有特殊情况，我补笔记了。',
            '我缺席了，能说一下那节课的重点吗？',
            '对不起，能说一下我错过了什么吗？',
            '我不是故意的，下次一定到。',
          ]},
          { t:['听不懂','不会','不明白','没学会','困惑'], r:[
            '这里我理解得不够，能再解释一下吗？',
            '我卡在这个点上了，请问方便说一下吗？',
            '老师您有推荐的学习资料吗？',
            '我课下再研究一下，有问题能来问您吗？',
          ]},
          { t:['迟到','晚到','来晚了'], r:[
            '老师对不起，来晚了，下次不会了。',
            '抱歉打扰了，我先坐下。',
          ]},
          { t:['论文','报告','答辩','截止'], r:[
            '我想提前请教一下方向，方便吗？',
            '有几个问题想确认一下，能占用您几分钟吗？',
            '能帮我看一下大纲方向对不对吗？',
          ]},
        ],
        def:[
          '谢谢老师，我记下了，会认真改进的。',
          '老师说得对，是我没做好。',
          '我回去再想想，有问题再来请教。',
          '我理解老师的意思，我来调整一下。',
          '谢谢您的反馈，对我很有帮助。',
          '我会认真对待这门课的。',
          '收到，我按照这个方向来。',
          '谢谢老师抽时间解答。',
          '我会加把劲的，谢谢您。',
          '明白了，感谢老师的指导。',
        ]
      },

      甲方: {
        keywords: [
          { t:['改','重做','方向不对','不满意','不好看','不喜欢'], r:[
            '收到，请问有参考方向吗？',
            '能说一下哪里不对，我来针对性调整。',
            '明白，这次的核心诉求是什么？',
            '好，改之前确认一下最终方向？',
            '可以改，能多说几个字吗，改起来更准。',
            '收到反馈，预计今天内出新版给您。',
          ]},
          { t:['加急','今晚','明天','赶紧','快点','尽快'], r:[
            '好的，最快今晚给您一版，确认一下需求？',
            '收到，我来加急，预计几点前需要？',
            '没问题，我重新排期，优先处理这个。',
            '加急可以，需要确认一下范围不变？',
          ]},
          { t:['便宜','少一点','打折','预算不够','能不能免费'], r:[
            '我来看看有没有调整空间，但需求要对应调整。',
            '预算和需求我们对齐一下，找个平衡点。',
            '这个报价是按范围算的，能说说哪块可以缩减？',
            '我理解预算压力，我们聊聊具体方案。',
          ]},
          { t:['别人家','竞品','同行','人家是这样的'], r:[
            '我了解一下，您说的案例能发给我看看吗？',
            '收到，我们来对比一下差异在哪。',
            '可以参考，但我们来看看更适合您的方向。',
          ]},
          { t:['版权','归属','知识产权','能不能用'], r:[
            '合同里有约定，我来给您确认一下。',
            '这块需要正式确认，走合同流程？',
          ]},
          { t:['不付款','延迟','尾款','什么时候打'], r:[
            '能确认一下款项的时间节点吗？',
            '我这边在跟进付款的事，能给个进度吗？',
            '合同约定的时间快到了，麻烦安排一下？',
          ]},
        ],
        def:[
          '收到，我整理一下再给您回复。',
          '明白，尽快跟进。',
          '好的，我来推进，有问题随时联系。',
          '这个需求我记下了，给我一点时间整理方案。',
          '收到，我们对齐一下再动。',
          '没问题，有结果马上同步您。',
          '好的，我今天内回复您。',
          '收到您的反馈，我来处理。',
          '明白需求，我这边安排上。',
          '好，我给您一个时间节点。',
        ]
      },

      室友: {
        keywords: [
          { t:['吵','噪音','声音大','太响','打扰到'], r:[
            '能小声一点吗，我这边有点受影响。',
            '不好意思说一下，能注意一下音量吗？',
            '不是不让你玩，就是声音稍微小点。',
            '我需要安静一下，能戴耳机吗？',
          ]},
          { t:['脏','乱','不打扫','公区','厨房','卫生'], r:[
            '公区的卫生我们能约定一下吗？',
            '能一起做个值日表吗，大家轮流。',
            '那个地方能收拾一下吗，我有点在意。',
            '说一声不是挑剔，就是一起住得舒服点。',
          ]},
          { t:['晚归','深夜','开灯','半夜','回来太晚'], r:[
            '能不能晚点轻一点，我睡眠比较浅。',
            '开灯没关系，能用小灯吗？',
            '不是怪你，就是说一声方便我知道。',
          ]},
          { t:['用我','拿我的','没问','借','我的东西'], r:[
            '用我的东西说一声就行，我不介意。',
            '借东西能提前问一下吗，不是不让用。',
            '下次说一声，我们都方便。',
          ]},
          { t:['气味','味道','臭','异味'], r:[
            '能说一下吗，公区有点味道。',
            '没别的意思，通风一下或者处理一下垃圾？',
          ]},
          { t:['客人','带人','有朋友来'], r:[
            '你带朋友来没问题，提前说一下我方便安排。',
            '没问题，说一声就行。',
          ]},
        ],
        def:[
          '说一声就行，我们好商量。',
          '住一起难免有摩擦，说出来比较好。',
          '我不是挑剔，说出来大家都舒服。',
          '我们约定一下？这样都方便。',
          '不是大事，说一下就解决了。',
          '我理解，我们找个都接受的方式。',
          '住一起就是要互相体谅。',
          '没关系，以后注意就行。',
          '不用放在心上，说出来就好。',
          '我们都好好的，有事说事。',
        ]
      },

      相亲: {
        keywords: [
          { t:['条件','要求','标准','必须','一定要'], r:[
            '我觉得人合适最重要，条件是其次。',
            '标准不是最关键的，看感觉。',
            '我没那么多硬条件，聊聊就知道合不合适。',
            '比起条件，我更在意日常相处。',
          ]},
          { t:['没感觉','不喜欢','不合适','不想发展'], r:[
            '感谢这次见面，我觉得可能不太合适。',
            '挺好的一个人，但感觉不是那个方向。',
            '我希望坦诚，我觉得我们更适合做朋友。',
            '真诚地说，我感觉没有走下去的那种感觉。',
          ]},
          { t:['工资','收入','多少钱','买房','有车','资产'], r:[
            '这些我觉得可以慢慢了解，先看看人合不合适。',
            '我对这些没有硬要求，感情是第一位的。',
            '物质基础重要，但不是唯一标准。',
          ]},
          { t:['催','什么意思','表态','有没有意向'], r:[
            '我喜欢慢慢来，给我们一点时间了解。',
            '还在观察，不急着做决定。',
            '感情的事急不了，再聊聊？',
          ]},
          { t:['继续','见面','发展','考虑一下'], r:[
            '我愿意继续了解，约个时间？',
            '感觉还不错，多见几次吧。',
            '挺好的，可以再聊聊。',
          ]},
          { t:['家里','父母','催','介绍'], r:[
            '家里催得急，但我希望是自然发展。',
            '不想因为催就随便，感情还是要认真对待。',
          ]},
        ],
        def:[
          '挺好的，慢慢了解吧。',
          '第一次见面紧张很正常，放松聊。',
          '感情的事不着急，慢慢来。',
          '我觉得相处舒不舒服最重要。',
          '多见几次才能了解，这次只是开始。',
          '我比较注重日常相处，聊聊生活？',
          '我觉得坦诚比表现重要。',
          '你有什么想了解我的，直接问。',
          '缘分这件事，顺其自然吧。',
          '我希望找个能说实话的人。',
        ]
      },
    },

    crazy: {
      同事: [
        '我已经是电量0%的手机了！！！还在被要求打电话！！！',
        '什么叫一个人当三个人用！！！这叫！！！',
        '我的理智已经提前一小时下班了！！！没有交接直接跑路！！！',
        '好的没问题！！！（内心已经爆炸第七次了）！！！',
        '我的黑眼圈是全公司最深的！！！这是我唯一的成就！！！',
        '你们知道我精神状态吗！！！是一坨被踩扁的薯片！！！碎了！！！',
        '什么叫合理安排时间！！！我哪里有时间！！！',
        '我已经麻了！！！彻底麻了！！！神经死了情绪也死了！！！',
        '正常上班不行吗！！！为什么！！！',
        '我要辞职！（但我不敢）我要躺平！（但我有贷款）啊啊啊！！！',
        '我的工位是我唯一的家！！！我住这了！！！',
        '今天崩溃三次！！！无声崩溃！！！含泪微笑崩溃！！！当面崩溃！！！',
        '谁来救救我！！！或者至少给我倒杯水！！！',
        '需求改了多少版了！！！我数不清了！！！脑子糊了！！！',
        '我是来上班的还是来挑战人类极限的！！！',
      ],
      领导: [
        '好的！！！（说不行也没用）！！！',
        '收到！！！（我要原地爆炸了）！！！',
        '明白！！！（完全不明白凭什么是我）！！！',
        '没问题！！！（问题大了去了）！！！',
        '我就是头牛！！！使劲薅！！！',
        '行！！！加就加！！！命不要了！！！',
        '我是ai吗！！！不用睡觉不用吃饭！！！',
        '您说的对！！！（我心里十万头羊驼）！！！',
        '好嘞！！！（双手颤抖接下任务）！！！',
        '随时都行！！！（睡觉时间不算）！！！',
        '收到！！！我已经忘了正常下班是什么感觉！！！',
        '您放心！！！（我不放心）！！！',
        '没有做不到只有累死了！！！',
        '听您的！！！（但我内心在哭泣）！！！',
        '我来！！！反正也没人能替我！！！',
      ],
      家人: [
        '你们知道我有多累吗！！！我已经在拼命了！！！',
        '我都这么大了！！！能不能别管这个！！！',
        '你们说一次我崩溃一次知道吗！！！',
        '我知道是好意但我真的撑不住这种好意了！！！',
        '爱我就让我喘口气好吗！！！拜托了！！！',
        '我是你们孩子！！！不是你们的焦虑容器！！！',
        '我也想嫁/娶！！！但婚姻大事急不来！！！你们急有用吗！！！',
        '我在外面已经很辛苦了！！！回家能不能歇一下！！！',
        '我不是不孝！！！我只是需要空间！！！',
        '催催催！！！催出一个好的来给你们看看！！！',
        '我的人生能不能让我自己过！！！',
        '我已经焦虑到不行了！！！你们再催我要爆了！！！',
        '我想哭！！！但哭了你们更担心！！！两难！！！',
        '我很努力了！！！真的！！！',
        '让我先活下去好吗！！！其他以后再说！！！',
      ],
      伴侣: [
        '你根本不懂我！！！我说了多少次了！！！',
        '我的眼泪已经哭干了！！！库存告急！！！',
        '我快被逼疯了！！！真的！！！',
        '你知道我为这段感情付出多少吗！！！',
        '我现在的心情你感受不到是吗！！！',
        '我要爆炸了！！！你看得出来吗！！！',
        '好啊你赢了！！！我不争了！！！我累了！！！',
        '为什么我总是要先道歉！！！',
        '我是不是隐形的！！！你好像看不见我！！！',
        '我的委屈放哪了！！！没地方放！！！',
        '爱一个人要这么累吗！！！',
        '我说了我说了我说了！！！你有没有在听！！！',
        '我不是你的出气筒！！！我也有情绪！！！',
        '我需要被照顾！！！不只是照顾你！！！',
        '算了算了！！！（但没有算了就是还在意）！！！',
      ],
      朋友: [
        '我就是个24小时待机的工具人是吗！！！',
        '我的底线在哪你们真的不知道吗！！！',
        '从现在开始我要对自己好一点！！！不伺候了！！！',
        '友情的代价是我全部精力吗！！！',
        '我以为你懂我！！！我错了！！！',
        '我是朋友！！！不是万能解决机！！！',
        '我也需要被关心！！！有没有人问过我还好吗！！！',
        '我的情绪价值谁来给我！！！',
        '帮到最后帮到我自己崩了！！！这合理吗！！！',
        '需要我就出现！！！不需要就消失！！！我不是外卖！！！',
        '我撑不住了！！！我也要人帮我！！！',
        '算了我自己消化！！！（消化不了的）！！！',
        '感情是双向的！！！有没有听说过！！！',
        '我要休息了！！！社交电量归零！！！',
        '我爱你们但我需要边界！！！',
      ],
      老师: [
        '我的GPA已经是一地碎片了！！！',
        '我真的学不会了！！！我就是块朽木！！！',
        '我看了八遍还是不会！！！救救我！！！',
        '我的脑子是空的！！！什么都装不进去！！！',
        '完蛋了！！！这门课我彻底跟不上了！！！',
        '期末了！！！我现在才开始复习！！！太晚了！！！',
        '我的考试焦虑已经严重影响到我考试了！！！',
        '为什么我看懂了还是不会做题！！！原理呢！！！',
        '书看了忘！！！忘了看！！！看了还是忘！！！',
        '我不是不努力！！！就是没用！！！',
        '挂科了！！！我的人生！！！',
        '老师给我一点点机会！！！一点点！！！',
        '我已经学到脑壳疼了！！！还是不会！！！',
        '我太难了！！！太难了！！！',
        '考试那天我能消失吗！！！',
      ],
      甲方: [
        '改了多少版了！！！！我数不清了！！！',
        '需求说了改！！！改了再改！！！改回第一版！！！',
        '我的创意在哪里死去！！！就在这份改稿单上！！！',
        '好的没问题！！！（泪水在眼眶里转）！！！',
        '您说的都对！！！（脑仁已经碎了）！！！',
        '再改一版！！！这是我今天听到最可怕的话！！！',
        '我的灵感已经和我离婚了！！！',
        '方向已经改了八百次！！！我找不到北了！！！',
        '明天早上！！！是哪个明天的早上！！！',
        '我快不行了！！！但稿子还没到位！！！',
        '免费的最贵！！！改到我自费住院！！！',
        '我很专业的！！！求您信任我一次！！！',
        '再这样下去我要改行了！！！',
        '参考案例发过来了！！！抄的话您自己抄！！！',
        '我的方案被否了！！！但他们做出来的就是我否掉的那版！！！',
      ],
      室友: [
        '我在自己家里变得如此不自在！！！',
        '我只想安静一下！！！一下下！！！',
        '公区的东西不是我专属清洁工！！！',
        '我晚上要睡觉！！！正常需求！！！',
        '我的耐心已经到极限了！！！说一遍了！！！',
        '住在一起不代表我要忍受一切！！！',
        '我的东西为什么要共享！！！没问过我！！！',
        '能不能把垃圾扔掉！！！我要发疯了！！！',
        '我要把门关上！！！永远关上！！！',
        '合租这件事我后悔了！！！',
        '为什么我要说三遍！！！',
        '我今天会住在图书馆！！！因为那里安静！！！',
        '我想要一个自己的房间！！！哭了！！！',
        '卫生间！！！为什么！！！',
        '我只是一个想好好睡觉的普通人！！！',
      ],
      相亲: [
        '我不想来！！！但我来了！！！',
        '我妈觉得我们合适！！！但我妈不是我！！！',
        '好人！！！好人！！！但不是我的好人！！！',
        '为什么相亲像面试！！！我来的是咖啡馆！！！',
        '彼此彼此！！！互相放过！！！',
        '我的要求不高！！！只要真的喜欢对方！！！这很难吗！！！',
        '相了多少次了！！！我快对感情祛魅了！！！',
        '对方条件很好！！！但我的心动呢！！！',
        '为什么要这么多条件！！！找工作吗！！！',
        '我只是想找个真的喜欢的人！！！有这么难吗！！！',
        '我相亲相到怀疑人生了！！！',
        '缘分你在哪！！！快来！！！我很着急！！！',
        '我妈比我还焦虑！！！我已经被传染了！！！',
        '好的今天又没有！！！下一个！！！',
        '算了算了！！！爱来不来！！！（但还是想来）！！！',
      ],
    }
  };

  /* ═══════════ State ═══════════ */
  let currentRole = '同事';
  let isLoading   = false;
  let lastResult  = '';
  let lastMode    = 'high';

  /* ═══════════ Role Tabs + Sliding Indicator ═══════════ */
  function updateIndicator(activeTab) {
    const ind  = document.getElementById('roleIndicator');
    const wrap = document.getElementById('roleWrap');
    const wRect = wrap.getBoundingClientRect();
    const tRect = activeTab.getBoundingClientRect();
    ind.style.left  = (tRect.left - wRect.left + wrap.scrollLeft) + 'px';
    ind.style.width = tRect.width + 'px';
  }

  document.querySelectorAll('.role-tab').forEach(btn => {
    btn.addEventListener('click', () => {
      document.querySelectorAll('.role-tab').forEach(b => b.classList.remove('active'));
      btn.classList.add('active');
      currentRole = btn.dataset.role;
      updateIndicator(btn);
      resetOutput();
      // Update placeholder
      document.getElementById('inputText').placeholder = placeholders[currentRole] || '说说你的真实想法...';
    });
  });

  const placeholders = {
    同事: '比如：这个需求我不想改了...',
    领导: '比如：凭什么又是我...',
    家人: '比如：别再催我了...',
    伴侣: '比如：你根本不理解我...',
    朋友: '比如：你只在有事的时候找我...',
    老师: '比如：作业真的没写完...',
    甲方: '比如：改了十几版了还要改...',
    室友: '比如：能不能别这么吵...',
    相亲: '比如：真的没感觉，想撤...',
  };

  // Init indicator on load
  window.addEventListener('load', () => {
    const first = document.querySelector('.role-tab.active');
    if (first) updateIndicator(first);
  });

  /* ═══════════ Logic ═══════════ */
  function pick(arr) { return arr[Math.floor(Math.random() * arr.length)]; }

  function getHighText(input) {
    const d = DATA.high[currentRole];
    if (!d) return '我需要一点时间想一想再回应。';
    for (const item of d.keywords) {
      if (item.t.some(k => input.includes(k))) return pick(item.r);
    }
    return pick(d.def);
  }

  function getCrazyText() {
    const pool = DATA.crazy[currentRole] || DATA.crazy['同事'];
    return pick(pool);
  }

  async function translate(mode) {
    if (isLoading) return;
    const input = document.getElementById('inputText').value.trim();
    if (!input) {
      const ta = document.getElementById('inputText');
      ta.style.opacity = '0.5';
      ta.focus();
      setTimeout(() => { ta.style.opacity = '1'; }, 600);
      return;
    }

    lastMode = mode;
    isLoading = true;
    showState('loading');

    await sleep(120 + Math.random() * 130);

    const text = mode === 'high' ? getHighText(input) : getCrazyText();
    lastResult = text;

    const badge = document.getElementById('modeBadge');
    badge.innerHTML = mode === 'high'
      ? `<span class="badge badge-eq">高情商 · ${currentRole}</span>`
      : `<span class="badge badge-crazy">发疯版 · ${currentRole}</span>`;

    document.getElementById('resultText').textContent = '';
    showState('result');
    document.getElementById('stateResult').classList.add('result-enter');

    await typeWriter(document.getElementById('resultText'), text, 5);
    isLoading = false;
  }

  async function typeWriter(el, text, speed) {
    for (let i = 0; i <= text.length; i++) {
      el.textContent = text.slice(0, i);
      await sleep(speed + Math.random() * 5);
    }
  }

  function sleep(ms) { return new Promise(r => setTimeout(r, ms)); }

  function showState(s) {
    document.getElementById('statePlaceholder').style.display = s === 'placeholder' ? 'block' : 'none';
    document.getElementById('stateLoading').style.display     = s === 'loading'     ? 'block' : 'none';
    const res = document.getElementById('stateResult');
    res.style.display = s === 'result' ? 'flex' : 'none';
    if (s === 'result') res.classList.remove('result-enter');
  }

  function resetOutput() {
    lastResult = ''; isLoading = false;
    showState('placeholder');
  }

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

  /* ═══════════ Events ═══════════ */
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
</html>
