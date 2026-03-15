import { useState, useEffect, useRef } from "react";

// ── data ──────────────────────────────────────────────
const TYPING = [
  "AI & Machine Learning Undergraduate 🤖",
  "2nd Year @ Sanjivani University 🎓",
  "Open Source Contributor 🌟",
  "Exploring Deep Learning & MLOps 🚀",
  "Building Real-World Projects 🛠️",
];

const SKILLS = {
  "💻 Languages": [
    { name: "Python",      color: "#3776AB", icon: "https://skillicons.dev/icons?i=python" },
    { name: "C++",         color: "#00599C", icon: "https://skillicons.dev/icons?i=cpp"    },
    { name: "Java",        color: "#ED8B00", icon: "https://skillicons.dev/icons?i=java"   },
    { name: "R",           color: "#276DC3", icon: "https://skillicons.dev/icons?i=r"      },
  ],
  "🌐 Web": [
    { name: "HTML5",       color: "#E34F26", icon: "https://skillicons.dev/icons?i=html"   },
    { name: "CSS3",        color: "#1572B6", icon: "https://skillicons.dev/icons?i=css"    },
    { name: "JavaScript",  color: "#F7DF1E", icon: "https://skillicons.dev/icons?i=js"     },
  ],
  "🗄️ Databases": [
    { name: "MySQL",       color: "#4479A1", icon: "https://skillicons.dev/icons?i=mysql"  },
    { name: "MongoDB",     color: "#47A248", icon: "https://skillicons.dev/icons?i=mongodb"},
  ],
  "⚙️ DevOps": [
    { name: "Git",         color: "#F05032", icon: "https://skillicons.dev/icons?i=git"    },
    { name: "GitHub",      color: "#ffffff", icon: "https://skillicons.dev/icons?i=github" },
    { name: "Docker",      color: "#2496ED", icon: "https://skillicons.dev/icons?i=docker" },
    { name: "VS Code",     color: "#007ACC", icon: "https://skillicons.dev/icons?i=vscode" },
  ],
  "📊 Data": [
    { name: "Jupyter",     color: "#F37626", icon: "https://skillicons.dev/icons?i=jupyter"},
    { name: "NumPy",       color: "#013243", icon: "https://skillicons.dev/icons?i=numpy"  },
    { name: "Pandas",      color: "#150458", icon: null },
    { name: "Power BI",    color: "#F2C811", icon: null },
  ],
};

const LANGS = [
  { name: "Python",          pct: 42, color: "#3776AB" },
  { name: "Jupyter Notebook",pct: 28, color: "#F37626" },
  { name: "C++",             pct: 15, color: "#00599C" },
  { name: "JavaScript",      pct: 8,  color: "#F7DF1E" },
  { name: "HTML/CSS",        pct: 5,  color: "#E34F26" },
  { name: "Other",           pct: 2,  color: "#4B5563" },
];

const LEARNING = [
  { topic: "Deep Learning & Neural Networks", status: "In Progress",  goal: "Build end-to-end ML models",        emoji: "🤖", pct: 40 },
  { topic: "Data Structures & Algorithms",    status: "In Progress",  goal: "Crack technical interviews",        emoji: "📐", pct: 55 },
  { topic: "Full-Stack Development",          status: "In Progress",  goal: "Build complete web apps",           emoji: "🌐", pct: 35 },
  { topic: "Docker & MLOps",                  status: "Exploring",    goal: "Deploy ML models to production",    emoji: "🐳", pct: 20 },
  { topic: "Data Science & EDA",              status: "Ongoing",      goal: "Real-world data analysis",          emoji: "📊", pct: 60 },
];

const STATUS_COLORS = {
  "In Progress": "#58A6FF",
  "Exploring":   "#F2C811",
  "Ongoing":     "#3FB950",
};

const PROJECTS = [
  { name: "ML Sentiment Analyser",  desc: "NLP model for Twitter sentiment classification using Python & scikit-learn.",   stars: 4,  lang: "Python",     langColor: "#3776AB" },
  { name: "Student Grade Predictor", desc: "Regression model predicting academic performance from behavioural features.",  stars: 3,  lang: "Jupyter",    langColor: "#F37626" },
  { name: "Portfolio Website",       desc: "Responsive personal portfolio built with HTML, CSS & vanilla JavaScript.",     stars: 2,  lang: "JavaScript", langColor: "#F7DF1E" },
  { name: "SQL Analytics Dashboard", desc: "MySQL queries + Power BI visuals for e-commerce sales analysis.",              stars: 5,  lang: "SQL",        langColor: "#4479A1" },
];

// ── hooks ─────────────────────────────────────────────
function useTyping(strings, speed = 55, pause = 2000) {
  const [text, setText] = useState("");
  const [idx, setIdx] = useState(0);
  const [ci, setCi] = useState(0);
  const [del, setDel] = useState(false);
  useEffect(() => {
    const cur = strings[idx];
    let t;
    if (!del && ci < cur.length)        t = setTimeout(() => setCi(c => c + 1), speed);
    else if (!del && ci === cur.length) t = setTimeout(() => setDel(true), pause);
    else if (del && ci > 0)             t = setTimeout(() => setCi(c => c - 1), speed / 2);
    else { setDel(false); setIdx(i => (i + 1) % strings.length); }
    setText(cur.slice(0, ci));
    return () => clearTimeout(t);
  }, [ci, del, idx]);
  return text;
}

function useCountUp(target, dur = 1400, run = false) {
  const [v, setV] = useState(0);
  useEffect(() => {
    if (!run) return;
    let start = null;
    const step = ts => {
      if (!start) start = ts;
      const p = Math.min((ts - start) / dur, 1);
      setV(Math.floor(p * target));
      if (p < 1) requestAnimationFrame(step);
    };
    requestAnimationFrame(step);
  }, [run, target]);
  return v;
}

function useInView(ref) {
  const [vis, setVis] = useState(false);
  useEffect(() => {
    const ob = new IntersectionObserver(([e]) => { if (e.isIntersecting) setVis(true); }, { threshold: 0.2 });
    if (ref.current) ob.observe(ref.current);
    return () => ob.disconnect();
  }, []);
  return vis;
}

// ── small components ──────────────────────────────────
function Stat({ label, value, icon, run }) {
  const n = useCountUp(value, 1300, run);
  return (
    <div style={{ textAlign: "center", flex: 1, minWidth: 80 }}>
      <div style={{ fontSize: 22 }}>{icon}</div>
      <div style={{ fontSize: 26, fontWeight: 800, color: "#58A6FF", fontFamily: "monospace" }}>{n}+</div>
      <div style={{ fontSize: 11, color: "#8B949E", textTransform: "uppercase", letterSpacing: 1, marginTop: 2 }}>{label}</div>
    </div>
  );
}

function SkillChip({ s }) {
  const [hov, setHov] = useState(false);
  return (
    <div
      onMouseEnter={() => setHov(true)}
      onMouseLeave={() => setHov(false)}
      style={{
        display: "flex", alignItems: "center", gap: 8,
        padding: "7px 14px", borderRadius: 10,
        background: hov ? `${s.color}22` : "rgba(255,255,255,0.04)",
        border: `1px solid ${hov ? s.color + "88" : "rgba(255,255,255,0.08)"}`,
        fontSize: 13, color: "#C9D1D9", cursor: "default",
        transition: "all 0.2s", transform: hov ? "translateY(-2px)" : "none",
        boxShadow: hov ? `0 4px 14px ${s.color}40` : "none",
      }}
    >
      {s.icon
        ? <img src={s.icon} alt={s.name} style={{ width: 18, height: 18 }} onError={e => e.target.style.display = "none"} />
        : <span style={{ width: 10, height: 10, borderRadius: "50%", background: s.color, display: "inline-block" }} />
      }
      <span style={{ fontWeight: 500 }}>{s.name}</span>
    </div>
  );
}

function ContribGrid() {
  const cols = 20, rows = 7;
  const data = Array.from({ length: cols }, () =>
    Array.from({ length: rows }, () => {
      const r = Math.random();
      return r > 0.7 ? 4 : r > 0.5 ? 3 : r > 0.3 ? 2 : r > 0.15 ? 1 : 0;
    })
  );
  const palette = ["#161B22", "#0E4429", "#006D32", "#26A641", "#39D353"];
  return (
    <div style={{ display: "flex", gap: 3 }}>
      {data.map((col, ci) => (
        <div key={ci} style={{ display: "flex", flexDirection: "column", gap: 3 }}>
          {col.map((lvl, ri) => (
            <div key={ri} style={{
              width: 11, height: 11, borderRadius: 2,
              background: palette[lvl],
              boxShadow: lvl >= 3 ? `0 0 5px ${palette[lvl]}` : "none",
              transition: "transform 0.15s",
              cursor: "pointer",
            }}
              onMouseEnter={e => e.currentTarget.style.transform = "scale(1.5)"}
              onMouseLeave={e => e.currentTarget.style.transform = "scale(1)"}
            />
          ))}
        </div>
      ))}
    </div>
  );
}

// ── main ──────────────────────────────────────────────
export default function App() {
  const typed = useTyping(TYPING);
  const [skillTab, setSkillTab] = useState("💻 Languages");
  const statsRef = useRef(null);
  const langsRef = useRef(null);
  const learnRef = useRef(null);
  const statsVis = useInView(statsRef);
  const langsVis = useInView(langsRef);
  const learnVis = useInView(learnRef);

  const S = (extra = {}) => ({
    background: "rgba(22,27,34,0.85)",
    border: "1px solid rgba(48,54,61,0.9)",
    borderRadius: 14,
    padding: 24,
    backdropFilter: "blur(12px)",
    marginBottom: 20,
    ...extra,
  });

  const STATS_DATA = [
    { label: "Repositories", value: 17, icon: "📁" },
    { label: "Commits",      value: 85, icon: "💾" },
    { label: "Stars Earned", value: 12, icon: "⭐" },
    { label: "Languages",    value: 9,  icon: "🌐" },
  ];

  return (
    <div style={{
      minHeight: "100vh",
      background: "#0D1117",
      color: "#E6EDF3",
      fontFamily: "-apple-system, 'Segoe UI', sans-serif",
      position: "relative",
    }}>
      {/* subtle grid bg */}
      <div style={{ position: "fixed", inset: 0, backgroundImage: "linear-gradient(rgba(48,54,61,0.15) 1px,transparent 1px),linear-gradient(90deg,rgba(48,54,61,0.15) 1px,transparent 1px)", backgroundSize: "40px 40px", pointerEvents: "none" }} />

      <div style={{ maxWidth: 860, margin: "0 auto", padding: "0 20px 80px", position: "relative" }}>

        {/* ── HERO ── */}
        <div style={{ textAlign: "center", padding: "56px 0 40px" }}>
          <div style={{ position: "relative", display: "inline-block", marginBottom: 20 }}>
            <div style={{
              width: 96, height: 96, borderRadius: "50%",
              background: "linear-gradient(135deg, #1F6FEB 0%, #58A6FF 50%, #3FB950 100%)",
              display: "flex", alignItems: "center", justifyContent: "center",
              fontSize: 44,
              boxShadow: "0 0 0 3px #0D1117, 0 0 0 5px #30363D, 0 0 24px rgba(31,111,235,0.35)",
            }}>👩‍💻</div>
            <div style={{ position: "absolute", bottom: 6, right: 6, width: 16, height: 16, background: "#3FB950", borderRadius: "50%", border: "2px solid #0D1117" }} />
          </div>

          <h1 style={{ fontSize: 40, fontWeight: 800, margin: "0 0 4px", letterSpacing: -0.5 }}>
            Ishwari Belhekar
          </h1>
          <div style={{ fontSize: 13, color: "#8B949E", letterSpacing: 2, textTransform: "uppercase", marginBottom: 16 }}>
            AIML • Sanjivani University • Batch 2028
          </div>

          <div style={{ fontSize: 15, color: "#58A6FF", fontFamily: "monospace", minHeight: 26, marginBottom: 24 }}>
            {typed}<span style={{ animation: "blink 1s step-end infinite", color: "#58A6FF" }}>▊</span>
          </div>

          {/* badge row */}
          <div style={{ display: "flex", flexWrap: "wrap", gap: 8, justifyContent: "center", marginBottom: 24 }}>
            {[
              { label: "🎓 B.Tech AIML",         bg: "#1F6FEB22", border: "#1F6FEB" },
              { label: "📍 Maharashtra, India",   bg: "#3FB95022", border: "#3FB950" },
              { label: "🌱 2nd Year",             bg: "#F2C81122", border: "#F2C811" },
              { label: "💼 Open to Internships",  bg: "#DA3633aa", border: "#F85149" },
            ].map(b => (
              <span key={b.label} style={{
                padding: "4px 14px", borderRadius: 20, fontSize: 12, fontWeight: 600,
                background: b.bg, border: `1px solid ${b.border}50`, color: "#C9D1D9",
              }}>{b.label}</span>
            ))}
          </div>

          {/* contact buttons */}
          <div style={{ display: "flex", gap: 10, justifyContent: "center", flexWrap: "wrap" }}>
            {[
              { label: "GitHub",   icon: "⚡", bg: "#21262D", border: "#30363D" },
              { label: "LinkedIn", icon: "💼", bg: "#0A66C2", border: "#0A66C2" },
              { label: "Email",    icon: "✉️", bg: "#21262D", border: "#30363D" },
            ].map(b => (
              <div key={b.label} style={{
                background: b.bg, border: `1px solid ${b.border}`,
                borderRadius: 8, padding: "7px 18px", fontSize: 13, fontWeight: 600,
                cursor: "pointer", display: "flex", alignItems: "center", gap: 6,
                transition: "opacity 0.15s, transform 0.15s",
              }}
                onMouseEnter={e => { e.currentTarget.style.opacity = "0.85"; e.currentTarget.style.transform = "translateY(-2px)"; }}
                onMouseLeave={e => { e.currentTarget.style.opacity = "1"; e.currentTarget.style.transform = "none"; }}
              >
                {b.icon} {b.label}
              </div>
            ))}
          </div>
        </div>

        {/* ── ABOUT – code card ── */}
        <div style={S()}>
          <div style={{ display: "flex", alignItems: "center", gap: 6, marginBottom: 14 }}>
            {["#FF5F57","#FEBC2E","#28C840"].map(c => <div key={c} style={{ width: 12, height: 12, borderRadius: "50%", background: c }} />)}
            <span style={{ marginLeft: 8, fontSize: 12, color: "#4B5563", fontFamily: "monospace" }}>ishwari.py</span>
          </div>
          <pre style={{ margin: 0, fontSize: 13, fontFamily: "'JetBrains Mono','Fira Code',monospace", lineHeight: 1.85, overflowX: "auto", color: "#E2E8F0" }}>
            <span style={{ color: "#FF7B72" }}>class </span><span style={{ color: "#D2A8FF" }}>IshwariBelhekar</span><span>:</span>{"\n"}
            {"    "}<span style={{ color: "#8B949E" }}>name</span>{"         = "}<span style={{ color: "#A5D6FF" }}>"Ishwari Belhekar"</span>{"\n"}
            {"    "}<span style={{ color: "#8B949E" }}>university</span>{"   = "}<span style={{ color: "#A5D6FF" }}>"Sanjivani University, Kopargaon"</span>{"\n"}
            {"    "}<span style={{ color: "#8B949E" }}>degree</span>{"       = "}<span style={{ color: "#A5D6FF" }}>"B.Tech – AI & Machine Learning"</span>{"\n"}
            {"    "}<span style={{ color: "#8B949E" }}>year</span>{"         = "}<span style={{ color: "#A5D6FF" }}>"2nd Year | Batch 2028"</span>{"\n"}
            {"    "}<span style={{ color: "#8B949E" }}>interests</span>{"    = ["}<span style={{ color: "#A5D6FF" }}>"ML/DL"</span>{", "}<span style={{ color: "#A5D6FF" }}>"Data Science"</span>{", "}<span style={{ color: "#A5D6FF" }}>"Web Dev"</span>{", "}<span style={{ color: "#A5D6FF" }}>"Open Source"</span>{"]\n"}
            {"    "}<span style={{ color: "#8B949E" }}>motto</span>{"        = "}<span style={{ color: "#A5D6FF" }}>"Learning by building. Growing by contributing."</span>
          </pre>
        </div>

        {/* ── STATS ── */}
        <div ref={statsRef} style={S()}>
          <h2 style={{ margin: "0 0 20px", fontSize: 17, display: "flex", alignItems: "center", gap: 8 }}>
            <span>📊</span> GitHub Statistics
          </h2>
          <div style={{ display: "flex", gap: 12, flexWrap: "wrap", marginBottom: 20, justifyContent: "space-around" }}>
            {STATS_DATA.map(s => <Stat key={s.label} {...s} run={statsVis} />)}
          </div>
          {/* GitHub stats card embed simulation */}
          <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 12 }}>
            {[
              { title: "GitHub Stats",        img: `https://github-readme-stats.vercel.app/api?username=ishwari418&show_icons=true&theme=github_dark&hide_border=true&bg_color=161B22&title_color=58A6FF&icon_color=58A6FF&text_color=C9D1D9` },
              { title: "Top Languages",       img: `https://github-readme-stats.vercel.app/api/top-langs/?username=ishwari418&layout=compact&theme=github_dark&hide_border=true&bg_color=161B22&title_color=58A6FF&text_color=C9D1D9` },
            ].map(c => (
              <div key={c.title} style={{ borderRadius: 10, overflow: "hidden", border: "1px solid #30363D" }}>
                <img src={c.img} alt={c.title} style={{ width: "100%", display: "block" }}
                  onError={e => {
                    e.target.style.display = "none";
                    e.target.nextSibling.style.display = "flex";
                  }}
                />
                <div style={{ display: "none", background: "#161B22", height: 100, alignItems: "center", justifyContent: "center", color: "#8B949E", fontSize: 12 }}>
                  {c.title} (loads on GitHub)
                </div>
              </div>
            ))}
          </div>
          {/* Streak */}
          <div style={{ marginTop: 12, borderRadius: 10, overflow: "hidden", border: "1px solid #30363D" }}>
            <img
              src="https://streak-stats.demolab.com?user=ishwari418&theme=github-dark-blue&hide_border=true&background=161B22&ring=58A6FF&fire=FF6B6B&currStreakLabel=58A6FF"
              alt="Streak" style={{ width: "100%", display: "block" }}
              onError={e => {
                e.target.style.display = "none";
                e.target.nextSibling.style.display = "flex";
              }}
            />
            <div style={{ display: "none", background: "#161B22", height: 80, alignItems: "center", justifyContent: "center", color: "#8B949E", fontSize: 12 }}>
              Contribution Streak (loads on GitHub)
            </div>
          </div>
        </div>

        {/* ── LANGUAGES + CONTRIB ── */}
        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 20, marginBottom: 20 }}>
          <div ref={langsRef} style={S({ marginBottom: 0 })}>
            <h2 style={{ margin: "0 0 18px", fontSize: 17, display: "flex", alignItems: "center", gap: 8 }}>
              🌐 Most Used Languages
            </h2>
            {LANGS.map((l, i) => {
              const [w, setW] = useState(0);
              useEffect(() => {
                if (!langsVis) return;
                const t = setTimeout(() => setW(l.pct), i * 100 + 100);
                return () => clearTimeout(t);
              }, [langsVis]);
              return (
                <div key={l.name} style={{ marginBottom: 10 }}>
                  <div style={{ display: "flex", justifyContent: "space-between", fontSize: 12, color: "#8B949E", marginBottom: 4 }}>
                    <span style={{ display: "flex", alignItems: "center", gap: 6 }}>
                      <span style={{ width: 9, height: 9, borderRadius: "50%", background: l.color, display: "inline-block" }} />
                      {l.name}
                    </span>
                    <span style={{ color: "#58A6FF", fontWeight: 600 }}>{l.pct}%</span>
                  </div>
                  <div style={{ background: "#21262D", borderRadius: 99, height: 6 }}>
                    <div style={{ width: `${w}%`, background: l.color, height: "100%", borderRadius: 99, transition: `width 0.8s cubic-bezier(.4,0,.2,1) ${i * 80}ms`, boxShadow: `0 0 6px ${l.color}70` }} />
                  </div>
                </div>
              );
            })}
          </div>
          <div style={S({ marginBottom: 0 })}>
            <h2 style={{ margin: "0 0 14px", fontSize: 17, display: "flex", alignItems: "center", gap: 8 }}>
              📈 Contributions
            </h2>
            <ContribGrid />
            <div style={{ marginTop: 10, display: "flex", alignItems: "center", gap: 4, fontSize: 11, color: "#4B5563" }}>
              <span>Less</span>
              {["#161B22","#0E4429","#006D32","#26A641","#39D353"].map(c => (
                <div key={c} style={{ width: 10, height: 10, borderRadius: 2, background: c }} />
              ))}
              <span>More</span>
            </div>
            <div style={{ marginTop: 16, padding: "12px 14px", background: "#161B22", borderRadius: 10, border: "1px solid #30363D" }}>
              <div style={{ fontSize: 12, color: "#8B949E", marginBottom: 8 }}>📅 This Year</div>
              <div style={{ display: "flex", gap: 16 }}>
                {[{ label: "Total Contributions", val: "183" }, { label: "Longest Streak", val: "17 days"
