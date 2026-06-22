import React, { useState, useEffect, useRef, useCallback } from 'react';

// ===== Design tokens =====
const tokens = {
  bg: '#EDE7D9',           // 和紙のような生成り
  bgDeep: '#E2DBC9',       // やや沈んだ和紙色
  card: '#F5F0E2',         // 紙の白
  ink: '#1B1B1F',          // 墨色
  inkSoft: '#4A4944',
  muted: '#8B8576',
  line: '#C9C0AC',
  accent: '#A8392B',       // 朱
  success: '#3A5847',      // 苔
};

const fontDisplay = '"Noto Serif JP", "Yu Mincho", "YuMincho", "Hiragino Mincho ProN", serif';
const fontBody = '"Hiragino Sans", "Yu Gothic", "Noto Sans JP", -apple-system, sans-serif';

const REQUIRED_CHARS = 2000;

// ===== Storage helpers =====
const storage = {
  async getSettings() {
    try {
      const r = await window.storage.get('diary:settings');
      return r ? JSON.parse(r.value) : { wakeUpTime: '07:00', soundOn: true };
    } catch {
      return { wakeUpTime: '07:00', soundOn: true };
    }
  },
  async setSettings(s) {
    await window.storage.set('diary:settings', JSON.stringify(s));
  },
  async getEntry(dateKey) {
    try {
      const r = await window.storage.get(`diary:entry:${dateKey}`);
      return r ? JSON.parse(r.value) : null;
    } catch { return null; }
  },
  async setEntry(dateKey, entry) {
    await window.storage.set(`diary:entry:${dateKey}`, JSON.stringify(entry));
  },
  async listEntries() {
    try {
      const r = await window.storage.list('diary:entry:');
      const keys = r?.keys || [];
      const entries = await Promise.all(keys.map(async (k) => {
        try {
          const v = await window.storage.get(k);
          return v ? { date: k.replace('diary:entry:', ''), ...JSON.parse(v.value) } : null;
        } catch { return null; }
      }));
      return entries.filter(Boolean).sort((a, b) => b.date.localeCompare(a.date));
    } catch { return []; }
  },
};

// ===== Time helpers =====
const pad = (n) => String(n).padStart(2, '0');
const dateKey = (d = new Date()) => `${d.getFullYear()}-${pad(d.getMonth() + 1)}-${pad(d.getDate())}`;
const hhmm = (d = new Date()) => `${pad(d.getHours())}:${pad(d.getMinutes())}`;
const formatDateJa = (d) => {
  const date = typeof d === 'string' ? new Date(d) : d;
  const w = ['日', '月', '火', '水', '木', '金', '土'][date.getDay()];
  return `${date.getFullYear()}年${date.getMonth() + 1}月${date.getDate()}日（${w}）`;
};

// ===== Sound (Web Audio gentle chime) =====
const playChime = () => {
  try {
    const ctx = new (window.AudioContext || window.webkitAudioContext)();
    const playNote = (freq, start, dur) => {
      const o = ctx.createOscillator();
      const g = ctx.createGain();
      o.type = 'sine';
      o.frequency.value = freq;
      g.gain.setValueAtTime(0, ctx.currentTime + start);
      g.gain.linearRampToValueAtTime(0.15, ctx.currentTime + start + 0.05);
      g.gain.exponentialRampToValueAtTime(0.001, ctx.currentTime + start + dur);
      o.connect(g);
      g.connect(ctx.destination);
      o.start(ctx.currentTime + start);
      o.stop(ctx.currentTime + start + dur);
    };
    // Soft chime sequence
    playNote(880, 0, 1.2);
    playNote(659, 0.4, 1.4);
    playNote(523, 0.8, 1.8);
  } catch (e) {
    // Audio not available
  }
};

// ===== Main App =====
export default function App() {
  const [view, setView] = useState('home'); // home | writing | history | settings
  const [now, setNow] = useState(new Date());
  const [settings, setSettings] = useState({ wakeUpTime: '07:00', soundOn: true });
  const [todayEntry, setTodayEntry] = useState(null);
  const [draft, setDraft] = useState('');
  const [entries, setEntries] = useState([]);
  const [loading, setLoading] = useState(true);
  const [showingHistoryDetail, setShowingHistoryDetail] = useState(null);
  const lastTriggeredRef = useRef(null);

  // Load initial data
  useEffect(() => {
    (async () => {
      const s = await storage.getSettings();
      setSettings(s);
      const t = await storage.getEntry(dateKey());
      setTodayEntry(t);
      setLoading(false);
    })();
  }, []);

  // Clock + alarm trigger
  useEffect(() => {
    const tick = async () => {
      const d = new Date();
      setNow(d);
      const currentKey = `${dateKey(d)} ${hhmm(d)}`;
      if (
        hhmm(d) === settings.wakeUpTime &&
        lastTriggeredRef.current !== currentKey &&
        view !== 'writing'
      ) {
        // Check if today's entry already exists
        const existing = await storage.getEntry(dateKey(d));
        if (!existing) {
          lastTriggeredRef.current = currentKey;
          triggerAlarm();
        }
      }
    };
    tick();
    const id = setInterval(tick, 1000);
    return () => clearInterval(id);
  }, [settings.wakeUpTime, view]);

  const triggerAlarm = () => {
    if (settings.soundOn) playChime();
    if ('Notification' in window && Notification.permission === 'granted') {
      try {
        new Notification('起床の時刻です', {
          body: '今朝の日記を書きましょう（2000文字）',
          silent: false,
        });
      } catch {}
    }
    setDraft('');
    setView('writing');
  };

  const requestNotificationPermission = useCallback(async () => {
    if ('Notification' in window && Notification.permission === 'default') {
      try { await Notification.requestPermission(); } catch {}
    }
  }, []);

  const saveSettings = async (s) => {
    setSettings(s);
    await storage.setSettings(s);
  };

  const submitEntry = async () => {
    if (draft.length < REQUIRED_CHARS) return;
    const entry = {
      content: draft,
      wakeUpTime: settings.wakeUpTime,
      writtenAt: new Date().toISOString(),
      chars: draft.length,
    };
    await storage.setEntry(dateKey(), entry);
    setTodayEntry(entry);
    setDraft('');
    setView('home');
  };

  const loadHistory = async () => {
    const list = await storage.listEntries();
    setEntries(list);
    setView('history');
  };

  // Reload today's entry when returning to home
  useEffect(() => {
    if (view === 'home') {
      (async () => {
        const t = await storage.getEntry(dateKey());
        setTodayEntry(t);
      })();
    }
  }, [view]);

  if (loading) {
    return (
      <div style={{ background: tokens.bg, fontFamily: fontBody, minHeight: '100vh' }} className="flex items-center justify-center">
        <div style={{ color: tokens.muted, fontFamily: fontDisplay }}>読み込み中…</div>
      </div>
    );
  }

  return (
    <div
      style={{ background: tokens.bg, color: tokens.ink, fontFamily: fontBody, minHeight: '100vh' }}
      className="w-full"
    >
      {view === 'home' && (
        <HomeView
          now={now}
          settings={settings}
          todayEntry={todayEntry}
          onOpenSettings={() => setView('settings')}
          onOpenHistory={loadHistory}
          onStartWriting={() => { setDraft(''); setView('writing'); }}
          onRequestNotif={requestNotificationPermission}
        />
      )}
      {view === 'writing' && (
        <WritingView
          draft={draft}
          setDraft={setDraft}
          onSubmit={submitEntry}
          now={now}
        />
      )}
      {view === 'settings' && (
        <SettingsView
          settings={settings}
          onSave={saveSettings}
          onBack={() => setView('home')}
          onRequestNotif={requestNotificationPermission}
        />
      )}
      {view === 'history' && (
        <HistoryView
          entries={entries}
          onBack={() => setView('home')}
          showingDetail={showingHistoryDetail}
          setShowingDetail={setShowingHistoryDetail}
        />
      )}
    </div>
  );
}

// ===== Home View =====
function HomeView({ now, settings, todayEntry, onOpenSettings, onOpenHistory, onStartWriting, onRequestNotif }) {
  useEffect(() => { onRequestNotif(); }, [onRequestNotif]);

  const currentTime = `${pad(now.getHours())}:${pad(now.getMinutes())}:${pad(now.getSeconds())}`;
  const written = !!todayEntry;

  // Calculate time until next wake
  const [wh, wm] = settings.wakeUpTime.split(':').map(Number);
  const nextWake = new Date(now);
  nextWake.setHours(wh, wm, 0, 0);
  if (nextWake <= now) nextWake.setDate(nextWake.getDate() + 1);
  const diff = nextWake - now;
  const hrsTo = Math.floor(diff / 3600000);
  const minsTo = Math.floor((diff % 3600000) / 60000);

  return (
    <div className="max-w-xl mx-auto px-6 py-10 sm:py-16">
      {/* Top eyebrow */}
      <div className="flex justify-between items-center mb-12 sm:mb-16">
        <div style={{ color: tokens.muted, letterSpacing: '0.2em' }} className="text-xs uppercase">
          朝の日記
        </div>
        <div style={{ color: tokens.muted }} className="text-xs tabular-nums">
          {formatDateJa(now)}
        </div>
      </div>

      {/* Hero */}
      <div className="mb-16">
        <div style={{ color: tokens.muted }} className="text-xs mb-3" >現在時刻</div>
        <div
          style={{ fontFamily: fontDisplay, color: tokens.ink, fontWeight: 300, letterSpacing: '-0.02em' }}
          className="text-6xl sm:text-7xl tabular-nums mb-12"
        >
          {currentTime}
        </div>

        {/* Status line */}
        <div className="border-t pt-6" style={{ borderColor: tokens.line }}>
          {written ? (
            <div>
              <div style={{ color: tokens.success, letterSpacing: '0.15em' }} className="text-xs uppercase mb-2">
                今朝の記録 ・ 完了
              </div>
              <div style={{ fontFamily: fontDisplay, color: tokens.ink }} className="text-2xl">
                {todayEntry.chars.toLocaleString()}文字を書きました
              </div>
              <div style={{ color: tokens.muted }} className="text-sm mt-1">
                {new Date(todayEntry.writtenAt).toLocaleTimeString('ja-JP', { hour: '2-digit', minute: '2-digit' })}に記録
              </div>
            </div>
          ) : (
            <div>
              <div style={{ color: tokens.muted, letterSpacing: '0.15em' }} className="text-xs uppercase mb-2">
                次の起床時刻
              </div>
              <div style={{ fontFamily: fontDisplay, color: tokens.ink }} className="text-3xl tabular-nums">
                {settings.wakeUpTime}
              </div>
              <div style={{ color: tokens.muted }} className="text-sm mt-2">
                あと {hrsTo > 0 ? `${hrsTo}時間` : ''}{minsTo}分で入力画面が開きます
              </div>
            </div>
          )}
        </div>
      </div>

      {/* Actions */}
      <div className="space-y-3 mb-12">
        {!written && (
          <button
            onClick={onStartWriting}
            style={{
              background: tokens.ink,
              color: tokens.bg,
              fontFamily: fontBody,
            }}
            className="w-full py-4 text-sm tracking-wider hover:opacity-90 transition"
          >
            今すぐ書きはじめる
          </button>
        )}
        <div className="grid grid-cols-2 gap-3">
          <button
            onClick={onOpenHistory}
            style={{ border: `1px solid ${tokens.line}`, color: tokens.ink }}
            className="py-3 text-sm tracking-wider hover:bg-black/5 transition"
          >
            これまでの記録
          </button>
          <button
            onClick={onOpenSettings}
            style={{ border: `1px solid ${tokens.line}`, color: tokens.ink }}
            className="py-3 text-sm tracking-wider hover:bg-black/5 transition"
          >
            設定
          </button>
        </div>
      </div>

      {/* Footnote */}
      <div style={{ color: tokens.muted, borderTop: `1px solid ${tokens.line}` }} className="text-xs pt-6 leading-relaxed">
        毎朝、設定時刻に入力画面が自動で開きます。<br />
        2000文字を書き終えるまで保存できません。
        <span style={{ color: tokens.accent }}>　— これがこの帳面の決まりごとです。</span>
      </div>
    </div>
  );
}

// ===== Writing View =====
function WritingView({ draft, setDraft, onSubmit, now }) {
  const textareaRef = useRef(null);
  const chars = draft.length;
  const progress = Math.min(chars / REQUIRED_CHARS, 1);
  const remaining = Math.max(REQUIRED_CHARS - chars, 0);
  const canSubmit = chars >= REQUIRED_CHARS;

  useEffect(() => {
    if (textareaRef.current) {
      textareaRef.current.focus();
    }
  }, []);

  return (
    <div className="max-w-2xl mx-auto px-6 py-8 sm:py-10">
      {/* Header */}
      <div className="flex items-baseline justify-between mb-6">
        <div>
          <div style={{ color: tokens.muted, letterSpacing: '0.2em' }} className="text-xs uppercase mb-1">
            今朝の記録
          </div>
          <div style={{ fontFamily: fontDisplay, color: tokens.ink }} className="text-xl">
            {formatDateJa(now)}
          </div>
        </div>
        <div style={{ color: tokens.muted }} className="text-xs tabular-nums">
          {pad(now.getHours())}:{pad(now.getMinutes())}
        </div>
      </div>

      {/* Counter — the signature element */}
      <div className="mb-4">
        <div className="flex items-baseline justify-between mb-2">
          <div className="flex items-baseline gap-3">
            <span
              style={{
                fontFamily: fontDisplay,
                color: canSubmit ? tokens.success : tokens.ink,
                fontWeight: 300,
                letterSpacing: '-0.02em',
              }}
              className="text-4xl sm:text-5xl tabular-nums"
            >
              {chars.toLocaleString()}
            </span>
            <span style={{ color: tokens.muted }} className="text-sm tabular-nums">
              / {REQUIRED_CHARS.toLocaleString()}
            </span>
          </div>
          <div style={{ color: tokens.muted }} className="text-xs">
            {canSubmit ? (
              <span style={{ color: tokens.success, letterSpacing: '0.1em' }}>完了 ・ 保存できます</span>
            ) : (
              <span>あと {remaining.toLocaleString()} 文字</span>
            )}
          </div>
        </div>
        {/* Progress bar */}
        <div
          style={{ background: tokens.bgDeep, height: '2px' }}
          className="w-full overflow-hidden"
        >
          <div
            style={{
              background: canSubmit ? tokens.success : tokens.ink,
              width: `${progress * 100}%`,
              height: '100%',
              transition: 'width 200ms ease-out, background 400ms',
            }}
          />
        </div>
      </div>

      {/* Textarea */}
      <textarea
        ref={textareaRef}
        value={draft}
        onChange={(e) => setDraft(e.target.value)}
        placeholder="今朝、目を覚まして最初に浮かんだことから書きはじめてください。考えがまとまっていなくて構いません。"
        style={{
          background: tokens.card,
          color: tokens.ink,
          fontFamily: fontBody,
          border: `1px solid ${tokens.line}`,
          lineHeight: 1.9,
          fontSize: '15px',
          minHeight: '60vh',
          resize: 'vertical',
        }}
        className="w-full p-6 outline-none focus:outline-none"
      />

      {/* Bottom action */}
      <div className="mt-6 flex items-center justify-between">
        <div style={{ color: tokens.muted }} className="text-xs">
          書きながら考える ・ 句読点も一文字
        </div>
        <button
          onClick={onSubmit}
          disabled={!canSubmit}
          style={{
            background: canSubmit ? tokens.accent : tokens.bgDeep,
            color: canSubmit ? tokens.card : tokens.muted,
            cursor: canSubmit ? 'pointer' : 'not-allowed',
            letterSpacing: '0.15em',
            transition: 'background 400ms',
          }}
          className="px-8 py-3 text-sm"
        >
          {canSubmit ? '今朝の日記を保存する' : '保存するには 2000文字'}
        </button>
      </div>
    </div>
  );
}

// ===== Settings View =====
function SettingsView({ settings, onSave, onBack, onRequestNotif }) {
  const [time, setTime] = useState(settings.wakeUpTime);
  const [soundOn, setSoundOn] = useState(settings.soundOn);

  const handleSave = async () => {
    await onSave({ wakeUpTime: time, soundOn });
    await onRequestNotif();
    onBack();
  };

  return (
    <div className="max-w-xl mx-auto px-6 py-10">
      <button onClick={onBack} style={{ color: tokens.muted }} className="text-xs mb-8 hover:text-black">
        ← もどる
      </button>

      <h2 style={{ fontFamily: fontDisplay, color: tokens.ink }} className="text-3xl mb-1">
        設定
      </h2>
      <div style={{ color: tokens.muted }} className="text-sm mb-12">
        起床時刻と通知の設定
      </div>

      {/* Wake time */}
      <div className="mb-10">
        <label style={{ color: tokens.muted, letterSpacing: '0.15em' }} className="block text-xs uppercase mb-3">
          起床時刻
        </label>
        <input
          type="time"
          value={time}
          onChange={(e) => setTime(e.target.value)}
          style={{
            background: tokens.card,
            border: `1px solid ${tokens.line}`,
            color: tokens.ink,
            fontFamily: fontDisplay,
            fontSize: '32px',
            letterSpacing: '0.05em',
          }}
          className="w-full px-5 py-4 tabular-nums outline-none"
        />
        <div style={{ color: tokens.muted }} className="text-xs mt-2 leading-relaxed">
          この時刻になると、自動で入力画面が開きます。<br />
          ブラウザのタブを閉じていると起動できません。
        </div>
      </div>

      {/* Sound */}
      <div className="mb-12">
        <label style={{ color: tokens.muted, letterSpacing: '0.15em' }} className="block text-xs uppercase mb-3">
          起床時のチャイム
        </label>
        <div className="flex gap-2">
          <button
            onClick={() => setSoundOn(true)}
            style={{
              background: soundOn ? tokens.ink : 'transparent',
              color: soundOn ? tokens.bg : tokens.ink,
              border: `1px solid ${soundOn ? tokens.ink : tokens.line}`,
            }}
            className="flex-1 py-3 text-sm tracking-wider"
          >
            鳴らす
          </button>
          <button
            onClick={() => setSoundOn(false)}
            style={{
              background: !soundOn ? tokens.ink : 'transparent',
              color: !soundOn ? tokens.bg : tokens.ink,
              border: `1px solid ${!soundOn ? tokens.ink : tokens.line}`,
            }}
            className="flex-1 py-3 text-sm tracking-wider"
          >
            鳴らさない
          </button>
        </div>
      </div>

      <button
        onClick={handleSave}
        style={{ background: tokens.ink, color: tokens.bg, letterSpacing: '0.15em' }}
        className="w-full py-4 text-sm hover:opacity-90 transition"
      >
        保存
      </button>
    </div>
  );
}

// ===== History View =====
function HistoryView({ entries, onBack, showingDetail, setShowingDetail }) {
  if (showingDetail) {
    return (
      <div className="max-w-2xl mx-auto px-6 py-10">
        <button
          onClick={() => setShowingDetail(null)}
          style={{ color: tokens.muted }}
          className="text-xs mb-8 hover:text-black"
        >
          ← 記録一覧へ
        </button>
        <div style={{ color: tokens.muted, letterSpacing: '0.15em' }} className="text-xs uppercase mb-2">
          {showingDetail.chars.toLocaleString()}文字 ・ {showingDetail.wakeUpTime} 起床
        </div>
        <h2 style={{ fontFamily: fontDisplay, color: tokens.ink }} className="text-2xl mb-8">
          {formatDateJa(showingDetail.date)}
        </h2>
        <div
          style={{
            background: tokens.card,
            border: `1px solid ${tokens.line}`,
            color: tokens.ink,
            lineHeight: 1.9,
            whiteSpace: 'pre-wrap',
            wordBreak: 'break-word',
          }}
          className="p-6 text-sm"
        >
          {showingDetail.content}
        </div>
      </div>
    );
  }

  return (
    <div className="max-w-xl mx-auto px-6 py-10">
      <button onClick={onBack} style={{ color: tokens.muted }} className="text-xs mb-8 hover:text-black">
        ← もどる
      </button>

      <h2 style={{ fontFamily: fontDisplay, color: tokens.ink }} className="text-3xl mb-1">
        これまでの記録
      </h2>
      <div style={{ color: tokens.muted }} className="text-sm mb-10">
        {entries.length}日分の朝
      </div>

      {entries.length === 0 ? (
        <div
          style={{ color: tokens.muted, border: `1px dashed ${tokens.line}` }}
          className="text-sm p-8 text-center"
        >
          まだ記録がありません。<br />
          最初の朝を書きはじめましょう。
        </div>
      ) : (
        <div style={{ borderTop: `1px solid ${tokens.line}` }}>
          {entries.map((e) => (
            <button
              key={e.date}
              onClick={() => setShowingDetail(e)}
              style={{ borderBottom: `1px solid ${tokens.line}` }}
              className="w-full text-left py-4 flex items-center justify-between hover:bg-black/5 transition px-2 -mx-2"
            >
              <div>
                <div style={{ fontFamily: fontDisplay, color: tokens.ink }} className="text-base mb-1">
                  {formatDateJa(e.date)}
                </div>
                <div style={{ color: tokens.muted }} className="text-xs">
                  {e.chars.toLocaleString()}文字 ・ {e.wakeUpTime} 起床
                </div>
              </div>
              <div style={{ color: tokens.muted }}>→</div>
            </button>
          ))}
        </div>
      )}
    </div>
  );
}
