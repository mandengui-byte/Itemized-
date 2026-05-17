# Itemized-
Contents Scanning App
# inventrack-App
Contents App
import { useState, useEffect } from "react";

// ─── DESIGN TOKENS ───────────────────────────────────────────────────────────
const theme = {
  bg: "#0A0E1A",
  bgCard: "#111827",
  bgElevated: "#1A2235",
  accent: "#00D4FF",
  accentGold: "#F5A623",
  accentGreen: "#00E5A0",
  accentRed: "#FF4D6A",
  text: "#F0F4FF",
  textMuted: "#8A9BC0",
  border: "#1E2D4A",
  borderGlow: "rgba(0,212,255,0.3)",
};

const css = `
  @import url('https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=DM+Sans:wght@300;400;500;600&family=JetBrains+Mono:wght@400;600&display=swap');

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    font-family: 'DM Sans', sans-serif;
    background: ${theme.bg};
    color: ${theme.text};
    min-height: 100vh;
    overflow-x: hidden;
  }

  ::-webkit-scrollbar { width: 4px; }
  ::-webkit-scrollbar-track { background: ${theme.bg}; }
  ::-webkit-scrollbar-thumb { background: ${theme.border}; border-radius: 2px; }

  .serif { font-family: 'DM Serif Display', serif; }
  .mono { font-family: 'JetBrains Mono', monospace; }

  @keyframes fadeIn { from { opacity:0; transform:translateY(16px); } to { opacity:1; transform:translateY(0); } }
  @keyframes pulse { 0%,100% { opacity:1; } 50% { opacity:0.5; } }
  @keyframes scanLine { 0% { top:0; } 100% { top:100%; } }
  @keyframes glow { 0%,100% { box-shadow: 0 0 10px rgba(0,212,255,0.2); } 50% { box-shadow: 0 0 30px rgba(0,212,255,0.6); } }
  @keyframes spin { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
  @keyframes slideIn { from { transform: translateX(-20px); opacity:0; } to { transform: translateX(0); opacity:1; } }

  .fade-in { animation: fadeIn 0.5s ease forwards; }
  .slide-in { animation: slideIn 0.4s ease forwards; }

  .btn {
    display: inline-flex; align-items: center; justify-content: center; gap: 8px;
    padding: 12px 24px; border-radius: 8px; font-family: 'DM Sans', sans-serif;
    font-size: 14px; font-weight: 600; cursor: pointer; border: none;
    transition: all 0.2s ease; letter-spacing: 0.02em;
  }
  .btn-primary {
    background: ${theme.accent}; color: #000;
  }
  .btn-primary:hover { background: #33DCFF; transform: translateY(-1px); box-shadow: 0 8px 24px rgba(0,212,255,0.4); }
  .btn-outline {
    background: transparent; color: ${theme.accent}; border: 1px solid ${theme.accent};
  }
  .btn-outline:hover { background: rgba(0,212,255,0.1); }
  .btn-ghost {
    background: transparent; color: ${theme.textMuted}; border: 1px solid ${theme.border};
  }
  .btn-ghost:hover { color: ${theme.text}; border-color: ${theme.textMuted}; }
  .btn-gold {
    background: ${theme.accentGold}; color: #000;
  }
  .btn-gold:hover { background: #F7B94A; transform: translateY(-1px); }
  .btn-danger {
    background: transparent; color: ${theme.accentRed}; border: 1px solid ${theme.accentRed};
  }
  .btn-danger:hover { background: rgba(255,77,106,0.1); }
  .btn-sm { padding: 8px 16px; font-size: 12px; }

  .card {
    background: ${theme.bgCard};
    border: 1px solid ${theme.border};
    border-radius: 12px;
    padding: 24px;
  }
  .card-hover:hover {
    border-color: rgba(0,212,255,0.4);
    transform: translateY(-2px);
    transition: all 0.2s ease;
    box-shadow: 0 8px 32px rgba(0,0,0,0.4);
    cursor: pointer;
  }

  input, select, textarea {
    background: ${theme.bgElevated};
    border: 1px solid ${theme.border};
    border-radius: 8px;
    color: ${theme.text};
    font-family: 'DM Sans', sans-serif;
    font-size: 14px;
    padding: 10px 14px;
    width: 100%;
    outline: none;
    transition: border-color 0.2s;
  }
  input:focus, select:focus, textarea:focus {
    border-color: ${theme.accent};
    box-shadow: 0 0 0 3px rgba(0,212,255,0.1);
  }
  select option { background: ${theme.bgCard}; }
  label {
    display: block; font-size: 12px; font-weight: 600;
    color: ${theme.textMuted}; text-transform: uppercase; letter-spacing: 0.08em;
    margin-bottom: 6px;
  }

  .stat-badge {
    display: inline-flex; align-items: center; gap: 6px;
    padding: 4px 10px; border-radius: 20px; font-size: 12px; font-weight: 600;
  }
  .badge-cyan { background: rgba(0,212,255,0.12); color: ${theme.accent}; }
  .badge-gold { background: rgba(245,166,35,0.12); color: ${theme.accentGold}; }
  .badge-green { background: rgba(0,229,160,0.12); color: ${theme.accentGreen}; }
  .badge-red { background: rgba(255,77,106,0.12); color: ${theme.accentRed}; }

  .divider { height: 1px; background: ${theme.border}; margin: 16px 0; }
  .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; }
  .grid-3 { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 16px; }
  .grid-4 { display: grid; grid-template-columns: repeat(4, 1fr); gap: 16px; }

  .tag {
    display: inline-block; padding: 2px 8px; border-radius: 4px;
    font-size: 11px; font-weight: 600; letter-spacing: 0.06em; text-transform: uppercase;
  }

  .modal-overlay {
    position: fixed; inset: 0; background: rgba(0,0,0,0.8); backdrop-filter: blur(8px);
    display: flex; align-items: center; justify-content: center; z-index: 1000; padding: 20px;
  }
  .modal {
    background: ${theme.bgCard}; border: 1px solid ${theme.border};
    border-radius: 16px; padding: 32px; max-width: 600px; width: 100%;
    max-height: 90vh; overflow-y: auto; animation: fadeIn 0.3s ease;
  }

  .progress-bar {
    height: 4px; background: ${theme.border}; border-radius: 2px; overflow: hidden;
  }
  .progress-fill {
    height: 100%; background: linear-gradient(90deg, ${theme.accent}, ${theme.accentGreen});
    border-radius: 2px; transition: width 0.5s ease;
  }

  .nav-item {
    display: flex; align-items: center; gap: 10px; padding: 10px 14px;
    border-radius: 8px; cursor: pointer; transition: all 0.2s;
    color: ${theme.textMuted}; font-size: 14px; font-weight: 500;
  }
  .nav-item:hover { background: rgba(255,255,255,0.05); color: ${theme.text}; }
  .nav-item.active { background: rgba(0,212,255,0.12); color: ${theme.accent}; }

  .scan-overlay {
    position: relative; overflow: hidden;
    border: 2px solid ${theme.accent};
    border-radius: 12px;
    animation: glow 2s ease-in-out infinite;
  }
  .scan-line {
    position: absolute; left: 0; right: 0; height: 2px;
    background: linear-gradient(90deg, transparent, ${theme.accent}, transparent);
    animation: scanLine 2s linear infinite;
    z-index: 10;
  }

  table { width: 100%; border-collapse: collapse; font-size: 13px; }
  th {
    text-align: left; padding: 10px 16px;
    color: ${theme.textMuted}; font-size: 11px; font-weight: 600;
    text-transform: uppercase; letter-spacing: 0.08em;
    border-bottom: 1px solid ${theme.border};
  }
  td { padding: 12px 16px; border-bottom: 1px solid rgba(30,45,74,0.5); }
  tr:hover td { background: rgba(255,255,255,0.02); }
`;

// ─── MOCK DATA ────────────────────────────────────────────────────────────────
const mockItems = {
  "Living Room": [
    { id: 1, name: "Samsung 65\" QLED TV", category: "Electronics", acv: 800, rcv: 1299, retailer: "Best Buy", retailerUrl: "https://bestbuy.com", serialNumber: "SN-ABC123", img: "📺", confirmed: true },
    { id: 2, name: "Leather Sofa (3-seater)", category: "Furniture", acv: 600, rcv: 1199, retailer: "IKEA", retailerUrl: "https://ikea.com", serialNumber: "", img: "🛋️", confirmed: true },
    { id: 3, name: "Coffee Table (Oak)", category: "Furniture", acv: 180, rcv: 349, retailer: "Wayfair", retailerUrl: "https://wayfair.com", serialNumber: "", img: "🪑", confirmed: false },
  ],
  "Kitchen": [
    { id: 4, name: "KitchenAid Stand Mixer", category: "Appliances", acv: 250, rcv: 449, retailer: "Amazon", retailerUrl: "https://amazon.com", serialNumber: "KA-78234", img: "🥣", confirmed: true },
    { id: 5, name: "Nespresso Coffee Machine", category: "Appliances", acv: 120, rcv: 199, retailer: "Amazon", retailerUrl: "https://amazon.com", serialNumber: "NES-3341", img: "☕", confirmed: true },
  ],
  "Master Bedroom": [
    { id: 6, name: "King Bed Frame (Walnut)", category: "Furniture", acv: 400, rcv: 899, retailer: "Restoration Hardware", retailerUrl: "https://rh.com", serialNumber: "", img: "🛏️", confirmed: true },
    { id: 7, name: "Diamond Engagement Ring", category: "Jewelry", acv: 3200, rcv: 4500, retailer: "Tiffany & Co.", retailerUrl: "https://tiffany.com", serialNumber: "", carats: "1.2", weight: "2.8g", clarity: "VS1", jeweler: "Tiffany & Co.", img: "💍", confirmed: true },
  ],
};

const subLimitOptions = ["Books", "Bicycles", "Fur", "Jewelry", "Tools", "Scheduled Items"];

// ─── ICONS (inline SVG as text) ──────────────────────────────────────────────
const Icon = ({ name, size = 16, color = "currentColor" }) => {
  const icons = {
    home: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><path d="m3 9 9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"/><polyline points="9 22 9 12 15 12 15 22"/></svg>,
    scan: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><rect x="3" y="3" width="5" height="5"/><rect x="16" y="3" width="5" height="5"/><rect x="3" y="16" width="5" height="5"/><line x1="21" y1="16" x2="21" y2="21"/><line x1="16" y1="21" x2="21" y2="21"/><line x1="16" y1="16" x2="16" y2="16"/></svg>,
    chart: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><line x1="18" y1="20" x2="18" y2="10"/><line x1="12" y1="20" x2="12" y2="4"/><line x1="6" y1="20" x2="6" y2="14"/></svg>,
    export: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="7 10 12 15 17 10"/><line x1="12" y1="15" x2="12" y2="3"/></svg>,
    settings: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><circle cx="12" cy="12" r="3"/><path d="M19.07 4.93a10 10 0 0 1 0 14.14M4.93 4.93a10 10 0 0 0 0 14.14"/></svg>,
    user: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"/><circle cx="12" cy="7" r="4"/></svg>,
    plus: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/></svg>,
    edit: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"/><path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"/></svg>,
    check: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2.5"><polyline points="20 6 9 17 4 12"/></svg>,
    arrow: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><line x1="5" y1="12" x2="19" y2="12"/><polyline points="12 5 19 12 12 19"/></svg>,
    x: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>,
    camera: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><path d="M23 19a2 2 0 0 1-2 2H3a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h4l2-3h6l2 3h4a2 2 0 0 1 2 2z"/><circle cx="12" cy="13" r="4"/></svg>,
    building: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><rect x="2" y="7" width="20" height="14" rx="2"/><path d="M16 21V5a2 2 0 0 0-2-2h-4a2 2 0 0 0-2 2v16"/></svg>,
    shield: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"/></svg>,
    logout: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"/><polyline points="16 17 21 12 16 7"/><line x1="21" y1="12" x2="9" y2="12"/></svg>,
    rooms: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"/></svg>,
    diamond: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><polygon points="12 2 22 8.5 22 15.5 12 22 2 15.5 2 8.5 12 2"/><line x1="12" y1="22" x2="12" y2="2"/><path d="M22 8.5H2"/></svg>,
    file: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/></svg>,
    tool: <svg width={size} height={size} viewBox="0 0 24 24" fill="none" stroke={color} strokeWidth="2"><path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z"/></svg>,
  };
  return icons[name] || null;
};

const fmt = (v) => `$${Number(v).toLocaleString("en-US", { minimumFractionDigits: 0 })}`;

// ─── COMPONENTS ──────────────────────────────────────────────────────────────

const StatCard = ({ label, value, sub, color = theme.accent, icon }) => (
  <div className="card" style={{ position: "relative", overflow: "hidden" }}>
    <div style={{ position: "absolute", top: 0, left: 0, right: 0, height: "2px", background: color }} />
    <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start" }}>
      <div>
        <div style={{ fontSize: 11, fontWeight: 700, color: theme.textMuted, textTransform: "uppercase", letterSpacing: "0.1em", marginBottom: 8 }}>{label}</div>
        <div className="mono" style={{ fontSize: 26, fontWeight: 600, color }}>{value}</div>
        {sub && <div style={{ fontSize: 12, color: theme.textMuted, marginTop: 4 }}>{sub}</div>}
      </div>
      <div style={{ padding: 10, background: `rgba(${color === theme.accentGold ? "245,166,35" : color === theme.accentGreen ? "0,229,160" : "0,212,255"},0.12)`, borderRadius: 8 }}>
        {icon}
      </div>
    </div>
  </div>
);

// ─── LANDING PAGE ─────────────────────────────────────────────────────────────
const LandingPage = ({ onSelect }) => {
  const [hovered, setHovered] = useState(null);

  return (
    <div style={{ minHeight: "100vh", display: "flex", flexDirection: "column", background: theme.bg, position: "relative", overflow: "hidden" }}>
      {/* Background grid */}
      <div style={{ position: "absolute", inset: 0, backgroundImage: `linear-gradient(rgba(0,212,255,0.03) 1px, transparent 1px), linear-gradient(90deg, rgba(0,212,255,0.03) 1px, transparent 1px)`, backgroundSize: "40px 40px", pointerEvents: "none" }} />
      <div style={{ position: "absolute", top: "-200px", right: "-200px", width: 600, height: 600, borderRadius: "50%", background: "radial-gradient(circle, rgba(0,212,255,0.08) 0%, transparent 70%)", pointerEvents: "none" }} />
      <div style={{ position: "absolute", bottom: "-100px", left: "-100px", width: 400, height: 400, borderRadius: "50%", background: "radial-gradient(circle, rgba(245,166,35,0.06) 0%, transparent 70%)", pointerEvents: "none" }} />

      {/* Header */}
      <div style={{ padding: "24px 48px", display: "flex", alignItems: "center", justifyContent: "space-between", borderBottom: `1px solid ${theme.border}` }}>
        <div style={{ display: "flex", alignItems: "center", gap: 12 }}>
          <div style={{ width: 36, height: 36, borderRadius: 8, background: `linear-gradient(135deg, ${theme.accent}, ${theme.accentGreen})`, display: "flex", alignItems: "center", justifyContent: "center" }}>
            <Icon name="home" size={18} color="#000" />
          </div>
          <div>
            <div className="serif" style={{ fontSize: 18, letterSpacing: "-0.01em" }}>InvenTrak <span style={{ color: theme.accent }}>AI</span></div>
            <div style={{ fontSize: 10, color: theme.textMuted, letterSpacing: "0.12em", textTransform: "uppercase" }}>Home Contents Intelligence</div>
          </div>
        </div>
        <div style={{ display: "flex", gap: 12 }}>
          <span style={{ fontSize: 13, color: theme.textMuted, display: "flex", alignItems: "center", gap: 6 }}>
            <span className="stat-badge badge-cyan">✦ AI-Powered</span>
          </span>
        </div>
      </div>

      {/* Hero */}
      <div style={{ flex: 1, display: "flex", flexDirection: "column", alignItems: "center", justifyContent: "center", padding: "60px 48px", textAlign: "center" }} className="fade-in">
        <div className="tag badge-cyan" style={{ marginBottom: 24, padding: "6px 16px" }}>Subscription-Based · Cross-Platform · Insurance Ready</div>
        <h1 className="serif" style={{ fontSize: 56, lineHeight: 1.1, marginBottom: 20, letterSpacing: "-0.02em", maxWidth: 700 }}>
          Your Home,<br /><span style={{ color: theme.accent }}>Fully Inventoried.</span>
        </h1>
        <p style={{ fontSize: 18, color: theme.textMuted, maxWidth: 520, lineHeight: 1.6, marginBottom: 48 }}>
          AI-powered room scanning, automatic valuations, and insurance-ready Excel reports — in minutes.
        </p>

        {/* User Type Selection */}
        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 20, width: "100%", maxWidth: 700 }}>
          {[
            { type: "homeowner", icon: "home", title: "Homeowner", sub: "Personal inventory & asset tracking", color: theme.accent, badge: "Basic / Pro" },
            { type: "enterprise", icon: "building", title: "Enterprise", sub: "Insurance, restoration & appraisal teams", color: theme.accentGold, badge: "Up to 200 devices" },
          ].map((opt) => (
            <div
              key={opt.type}
              className="card card-hover"
              onMouseEnter={() => setHovered(opt.type)}
              onMouseLeave={() => setHovered(null)}
              onClick={() => onSelect(opt.type)}
              style={{
                padding: 32, textAlign: "left", cursor: "pointer",
                borderColor: hovered === opt.type ? opt.color : theme.border,
                boxShadow: hovered === opt.type ? `0 0 32px rgba(${opt.color === theme.accentGold ? "245,166,35" : "0,212,255"},0.15)` : "none",
                transition: "all 0.25s ease",
              }}
            >
              <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start", marginBottom: 16 }}>
                <div style={{ width: 48, height: 48, borderRadius: 10, background: `rgba(${opt.color === theme.accentGold ? "245,166,35" : "0,212,255"},0.12)`, display: "flex", alignItems: "center", justifyContent: "center" }}>
                  <Icon name={opt.icon} size={22} color={opt.color} />
                </div>
                <span className="tag" style={{ background: `rgba(${opt.color === theme.accentGold ? "245,166,35" : "0,212,255"},0.12)`, color: opt.color }}>{opt.badge}</span>
              </div>
              <div className="serif" style={{ fontSize: 22, marginBottom: 8 }}>{opt.title}</div>
              <div style={{ fontSize: 13, color: theme.textMuted, lineHeight: 1.5, marginBottom: 20 }}>{opt.sub}</div>
              <div style={{ display: "flex", alignItems: "center", gap: 6, fontSize: 13, fontWeight: 600, color: opt.color }}>
                Sign in <Icon name="arrow" size={14} color={opt.color} />
              </div>
            </div>
          ))}
        </div>

        <p style={{ fontSize: 12, color: theme.textMuted, marginTop: 32 }}>
          Trusted by 12,000+ homeowners · Used by top insurance carriers
        </p>
      </div>
    </div>
  );
};

// ─── LOGIN PAGE ───────────────────────────────────────────────────────────────
const LoginPage = ({ userType, onLogin, onBack }) => {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [enterpriseRole, setEnterpriseRole] = useState("insurance");

  const isEnterprise = userType === "enterprise";

  return (
    <div style={{ minHeight: "100vh", display: "flex", alignItems: "center", justifyContent: "center", background: theme.bg, position: "relative" }}>
      <div style={{ position: "absolute", inset: 0, backgroundImage: `linear-gradient(rgba(0,212,255,0.03) 1px, transparent 1px), linear-gradient(90deg, rgba(0,212,255,0.03) 1px, transparent 1px)`, backgroundSize: "40px 40px" }} />

      <div className="card fade-in" style={{ width: "100%", maxWidth: 440, padding: 40, position: "relative" }}>
        <button onClick={onBack} style={{ background: "none", border: "none", color: theme.textMuted, cursor: "pointer", fontSize: 13, marginBottom: 24, display: "flex", alignItems: "center", gap: 6 }}>
          ← Back
        </button>

        <div style={{ display: "flex", alignItems: "center", gap: 12, marginBottom: 28 }}>
          <div style={{ width: 40, height: 40, borderRadius: 10, background: `linear-gradient(135deg, ${isEnterprise ? theme.accentGold : theme.accent}, ${theme.accentGreen})`, display: "flex", alignItems: "center", justifyContent: "center" }}>
            <Icon name={isEnterprise ? "building" : "home"} size={18} color="#000" />
          </div>
          <div>
            <div className="serif" style={{ fontSize: 20 }}>{isEnterprise ? "Enterprise" : "Homeowner"} Login</div>
            <div style={{ fontSize: 12, color: theme.textMuted }}>InvenTrak AI</div>
          </div>
        </div>

        {isEnterprise && (
          <div style={{ marginBottom: 20 }}>
            <label>Account Type</label>
            <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr 1fr", gap: 8 }}>
              {[
                { val: "insurance", label: "Insurance\nCarrier", icon: "shield" },
                { val: "restoration", label: "Restoration\nVendor", icon: "tool" },
                { val: "appraiser", label: "Appraiser", icon: "diamond" },
              ].map((r) => (
                <div key={r.val} onClick={() => setEnterpriseRole(r.val)} style={{ padding: "12px 8px", border: `1px solid ${enterpriseRole === r.val ? theme.accentGold : theme.border}`, borderRadius: 8, textAlign: "center", cursor: "pointer", background: enterpriseRole === r.val ? "rgba(245,166,35,0.08)" : "transparent", transition: "all 0.2s" }}>
                  <div style={{ marginBottom: 4 }}><Icon name={r.icon} size={16} color={enterpriseRole === r.val ? theme.accentGold : theme.textMuted} /></div>
                  <div style={{ fontSize: 11, fontWeight: 600, color: enterpriseRole === r.val ? theme.accentGold : theme.textMuted, whiteSpace: "pre-line" }}>{r.label}</div>
                </div>
              ))}
            </div>
          </div>
        )}

        <div style={{ display: "flex", flexDirection: "column", gap: 16 }}>
          <div>
            <label>Email Address</label>
            <input type="email" value={email} onChange={e => setEmail(e.target.value)} placeholder="you@example.com" />
          </div>
          <div>
            <label>Password</label>
            <input type="password" value={password} onChange={e => setPassword(e.target.value)} placeholder="••••••••" />
          </div>
          {isEnterprise && (
            <div style={{ padding: 12, background: "rgba(245,166,35,0.06)", borderRadius: 8, border: `1px solid rgba(245,166,35,0.2)`, fontSize: 12, color: theme.textMuted }}>
              🔐 Enterprise license supports up to <strong style={{ color: theme.accentGold }}>200 simultaneous devices</strong>
            </div>
          )}
          <button className="btn" style={{ width: "100%", background: isEnterprise ? theme.accentGold : theme.accent, color: "#000", marginTop: 8 }} onClick={() => onLogin(userType, enterpriseRole)}>
            Sign In <Icon name="arrow" size={14} color="#000" />
          </button>
        </div>

        <div style={{ marginTop: 20, textAlign: "center", fontSize: 12, color: theme.textMuted }}>
          Forgot password? <span style={{ color: theme.accent, cursor: "pointer" }}>Reset here</span>
        </div>
      </div>
    </div>
  );
};

// ─── ENTERPRISE CLIENT INTAKE ─────────────────────────────────────────────────
const EnterpriseIntake = ({ role, onComplete }) => {
  const [form, setForm] = useState({
    clientName: "", clientAddress: "", numResidents: "", residentAges: "",
    numRooms: "", numBedrooms: "", policyLimit: "", subLimits: {},
  });
  const [step, setStep] = useState(1);

  const set = (k, v) => setForm(f => ({ ...f, [k]: v }));
  const setSubLimit = (item, val) => setForm(f => ({ ...f, subLimits: { ...f.subLimits, [item]: val } }));

  const roleLabels = { insurance: "Insurance Carrier", restoration: "Restoration Vendor", appraiser: "Appraiser" };
  const roleColors = { insurance: theme.accentGreen, restoration: theme.accent, appraiser: theme.accentGold };
  const col = roleColors[role] || theme.accent;

  return (
    <div style={{ minHeight: "100vh", background: theme.bg, display: "flex" }}>
      {/* Sidebar */}
      <div style={{ width: 260, background: theme.bgCard, borderRight: `1px solid ${theme.border}`, padding: 32, display: "flex", flexDirection: "column" }}>
        <div style={{ display: "flex", alignItems: "center", gap: 10, marginBottom: 32 }}>
          <div style={{ width: 32, height: 32, borderRadius: 8, background: `linear-gradient(135deg, ${col}, ${theme.accentGreen})`, display: "flex", alignItems: "center", justifyContent: "center" }}>
            <Icon name="building" size={14} color="#000" />
          </div>
          <div className="serif" style={{ fontSize: 15 }}>InvenTrak AI</div>
        </div>
        <div style={{ fontSize: 11, color: theme.textMuted, textTransform: "uppercase", letterSpacing: "0.1em", marginBottom: 12 }}>Session Role</div>
        <div style={{ padding: "8px 12px", background: `rgba(${col === theme.accentGold ? "245,166,35" : col === theme.accentGreen ? "0,229,160" : "0,212,255"},0.1)`, borderRadius: 8, color: col, fontSize: 13, fontWeight: 600, marginBottom: 32 }}>
          {roleLabels[role]}
        </div>
        {[{ n: 1, l: "Client Info" }, { n: 2, l: "Property Details" }, { n: 3, l: "Policy & Sub-Limits" }].map(s => (
          <div key={s.n} style={{ display: "flex", alignItems: "center", gap: 12, padding: "10px 0", cursor: "pointer", color: step === s.n ? col : step > s.n ? theme.accentGreen : theme.textMuted }} onClick={() => setStep(s.n)}>
            <div style={{ width: 24, height: 24, borderRadius: "50%", border: `2px solid ${step === s.n ? col : step > s.n ? theme.accentGreen : theme.border}`, display: "flex", alignItems: "center", justifyContent: "center", fontSize: 11, fontWeight: 700, background: step > s.n ? theme.accentGreen : "transparent", color: step > s.n ? "#000" : "inherit" }}>
              {step > s.n ? "✓" : s.n}
            </div>
            <span style={{ fontSize: 13, fontWeight: 500 }}>{s.l}</span>
          </div>
        ))}
      </div>

      {/* Main */}
      <div style={{ flex: 1, padding: "48px", overflowY: "auto" }}>
        <div style={{ maxWidth: 600 }} className="fade-in">
          <div style={{ marginBottom: 32 }}>
            <h2 className="serif" style={{ fontSize: 32, marginBottom: 8 }}>New Client Intake</h2>
            <p style={{ color: theme.textMuted, fontSize: 15 }}>Step {step} of 3 — {["", "Client Information", "Property Details", "Policy & Sub-Limits"][step]}</p>
            <div className="progress-bar" style={{ marginTop: 16 }}>
              <div className="progress-fill" style={{ width: `${(step / 3) * 100}%` }} />
            </div>
          </div>

          {step === 1 && (
            <div style={{ display: "flex", flexDirection: "column", gap: 20 }}>
              <div><label>Client Full Name *</label><input value={form.clientName} onChange={e => set("clientName", e.target.value)} placeholder="Jane Doe" /></div>
              <div><label>Client Address *</label><input value={form.clientAddress} onChange={e => set("clientAddress", e.target.value)} placeholder="123 Maple St, Austin, TX 78701" /></div>
              <div className="grid-2">
                <div><label>Number of Residents</label><input type="number" value={form.numResidents} onChange={e => set("numResidents", e.target.value)} placeholder="4" min="1" /></div>
                <div><label>Ages of Residents</label><input value={form.residentAges} onChange={e => set("residentAges", e.target.value)} placeholder="42, 39, 17, 14" /></div>
              </div>
            </div>
          )}

          {step === 2 && (
            <div style={{ display: "flex", flexDirection: "column", gap: 20 }}>
              <div className="grid-2">
                <div><label>Total Number of Rooms</label><input type="number" value={form.numRooms} onChange={e => set("numRooms", e.target.value)} placeholder="8" min="1" /></div>
                <div><label>Number of Bedrooms</label><input type="number" value={form.numBedrooms} onChange={e => set("numBedrooms", e.target.value)} placeholder="3" min="1" /></div>
              </div>
              <div style={{ padding: 16, background: theme.bgElevated, borderRadius: 10, border: `1px solid ${theme.border}` }}>
                <div style={{ fontSize: 13, color: theme.textMuted, marginBottom: 12 }}>Room scan will be available on the dashboard after setup.</div>
                <div style={{ display: "flex", gap: 8, flexWrap: "wrap" }}>
                  {["Living Room", "Kitchen", "Master Bedroom", "Bedroom 2", "Bathroom", "Garage", "Office", "Dining Room"].map(r => (
                    <span key={r} className="tag badge-cyan" style={{ padding: "4px 10px", fontSize: 11 }}>{r}</span>
                  ))}
                </div>
              </div>
            </div>
          )}

          {step === 3 && (
            <div style={{ display: "flex", flexDirection: "column", gap: 20 }}>
              <div>
                <label>Policy Limit ($)</label>
                <div style={{ position: "relative" }}>
                  <span style={{ position: "absolute", left: 12, top: "50%", transform: "translateY(-50%)", color: theme.textMuted }}>$</span>
                  <input type="number" value={form.policyLimit} onChange={e => set("policyLimit", e.target.value)} placeholder="250,000" style={{ paddingLeft: 24 }} />
                </div>
              </div>
              <div>
                <label>Sub-Limits</label>
                <div style={{ display: "flex", flexDirection: "column", gap: 10, marginTop: 4 }}>
                  {subLimitOptions.map(item => (
                    <div key={item} style={{ display: "flex", alignItems: "center", gap: 12 }}>
                      <div style={{ width: 120, fontSize: 13, fontWeight: 500 }}>{item}</div>
                      <select value={form.subLimits[item] || ""} onChange={e => setSubLimit(item, e.target.value)} style={{ flex: 1 }}>
                        <option value="">No sub-limit</option>
                        <option value="1000">$1,000</option>
                        <option value="2500">$2,500</option>
                        <option value="5000">$5,000</option>
                        <option value="10000">$10,000</option>
                        <option value="25000">$25,000</option>
                        <option value="50000">$50,000</option>
                        <option value="custom">Custom...</option>
                      </select>
                    </div>
                  ))}
                </div>
              </div>
            </div>
          )}

          <div style={{ display: "flex", gap: 12, marginTop: 32 }}>
            {step > 1 && <button className="btn btn-ghost" onClick={() => setStep(s => s - 1)}>← Back</button>}
            {step < 3
              ? <button className="btn" style={{ background: col, color: "#000" }} onClick={() => setStep(s => s + 1)}>Continue →</button>
              : <button className="btn" style={{ background: col, color: "#000" }} onClick={() => onComplete(form)}>Begin Scanning →</button>
            }
          </div>
        </div>
      </div>
    </div>
  );
};

// ─── SCAN MODAL ───────────────────────────────────────────────────────────────
const ScanModal = ({ room, onClose, onAdd }) => {
  const [phase, setPhase] = useState("idle"); // idle | scanning | identified | form
  const [identified, setIdentified] = useState(null);
  const [form, setForm] = useState({});
  const categories = ["Electronics", "Furniture", "Appliances", "Jewelry", "Clothing", "Art", "Sports", "Tools", "Books"];

  const mockScan = () => {
    setPhase("scanning");
    setTimeout(() => {
      const item = {
        name: "Apple MacBook Pro 14\"",
        category: "Electronics",
        acv: 1200,
        rcv: 1999,
        retailer: "Apple",
        retailerUrl: "https://apple.com",
        img: "💻",
        isElectronics: true,
        isJewelry: false,
      };
      setIdentified(item);
      setForm({ ...item, serialNumber: "", carats: "", weight: "", clarity: "", jeweler: "" });
      setPhase("identified");
    }, 2500);
  };

  const set = (k, v) => setForm(f => ({ ...f, [k]: v }));

  return (
    <div className="modal-overlay">
      <div className="modal" style={{ maxWidth: 520 }}>
        <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 24 }}>
          <div className="serif" style={{ fontSize: 22 }}>Scan Item — <span style={{ color: theme.accent }}>{room}</span></div>
          <button onClick={onClose} style={{ background: "none", border: "none", cursor: "pointer", color: theme.textMuted }}><Icon name="x" size={20} /></button>
        </div>

        {phase === "idle" && (
          <div style={{ textAlign: "center" }}>
            <div style={{ width: "100%", height: 220, border: `2px dashed ${theme.border}`, borderRadius: 12, display: "flex", flexDirection: "column", alignItems: "center", justifyContent: "center", gap: 12, cursor: "pointer", marginBottom: 20, color: theme.textMuted, background: theme.bgElevated }} onClick={mockScan}>
              <Icon name="camera" size={36} color={theme.accent} />
              <div style={{ fontSize: 14 }}>Tap to photograph or upload image</div>
              <div style={{ fontSize: 12 }}>AI will identify item, estimate ACV & RCV</div>
            </div>
            <button className="btn btn-primary" style={{ width: "100%" }} onClick={mockScan}>
              <Icon name="camera" size={16} color="#000" /> Start AI Scan
            </button>
          </div>
        )}

        {phase === "scanning" && (
          <div className="scan-overlay" style={{ height: 220, display: "flex", flexDirection: "column", alignItems: "center", justifyContent: "center", gap: 12, background: theme.bgElevated }}>
            <div className="scan-line" />
            <div style={{ fontSize: 32 }}>📷</div>
            <div style={{ fontSize: 14, color: theme.accent, animation: "pulse 1.5s ease infinite" }}>AI Identifying Item...</div>
            <div style={{ fontSize: 12, color: theme.textMuted }}>Checking 40M+ product database</div>
          </div>
        )}

        {(phase === "identified" || phase === "form") && identified && (
          <div>
            <div style={{ display: "flex", gap: 16, padding: 16, background: "rgba(0,229,160,0.06)", border: `1px solid rgba(0,229,160,0.2)`, borderRadius: 10, marginBottom: 20 }}>
              <div style={{ fontSize: 36 }}>{identified.img}</div>
              <div>
                <div style={{ fontWeight: 600, marginBottom: 4 }}>{identified.name}</div>
                <div style={{ fontSize: 12, color: theme.textMuted }}>AI Confidence: <span style={{ color: theme.accentGreen }}>94%</span></div>
                <div style={{ display: "flex", gap: 8, marginTop: 8 }}>
                  <span className="stat-badge badge-cyan">ACV: {fmt(identified.acv)}</span>
                  <span className="stat-badge badge-gold">RCV: {fmt(identified.rcv)}</span>
                </div>
              </div>
            </div>

            <div style={{ display: "flex", flexDirection: "column", gap: 14 }}>
              <div className="grid-2">
                <div><label>Item Name</label><input value={form.name || ""} onChange={e => set("name", e.target.value)} /></div>
                <div><label>Category</label>
                  <select value={form.category || ""} onChange={e => set("category", e.target.value)}>
                    {categories.map(c => <option key={c}>{c}</option>)}
                  </select>
                </div>
              </div>
              <div className="grid-2">
                <div><label>ACV ($)</label><input type="number" value={form.acv || ""} onChange={e => set("acv", e.target.value)} /></div>
                <div><label>RCV ($)</label><input type="number" value={form.rcv || ""} onChange={e => set("rcv", e.target.value)} /></div>
              </div>
              <div className="grid-2">
                <div><label>Retailer</label><input value={form.retailer || ""} onChange={e => set("retailer", e.target.value)} /></div>
                <div><label>Purchase URL</label><input value={form.retailerUrl || ""} onChange={e => set("retailerUrl", e.target.value)} /></div>
              </div>

              {/* Electronics: Serial Number */}
              {(form.category === "Electronics" || form.category === "Appliances") && (
                <div style={{ padding: 14, background: "rgba(0,212,255,0.06)", border: `1px solid rgba(0,212,255,0.2)`, borderRadius: 8 }}>
                  <div style={{ fontSize: 12, fontWeight: 700, color: theme.accent, marginBottom: 10, display: "flex", alignItems: "center", gap: 6 }}>
                    🔢 Serial Number Required for Electronics
                  </div>
                  <input value={form.serialNumber || ""} onChange={e => set("serialNumber", e.target.value)} placeholder="Enter serial number (check device label)" />
                </div>
              )}

              {/* Jewelry: Extra fields */}
              {form.category === "Jewelry" && (
                <div style={{ padding: 14, background: "rgba(245,166,35,0.06)", border: `1px solid rgba(245,166,35,0.2)`, borderRadius: 8 }}>
                  <div style={{ fontSize: 12, fontWeight: 700, color: theme.accentGold, marginBottom: 10 }}>💎 Jewelry Details Required</div>
                  <div className="grid-2" style={{ gap: 10 }}>
                    <div><label>Carats</label><input value={form.carats || ""} onChange={e => set("carats", e.target.value)} placeholder="1.2" /></div>
                    <div><label>Weight (g)</label><input value={form.weight || ""} onChange={e => set("weight", e.target.value)} placeholder="2.8" /></div>
                    <div><label>Clarity</label><input value={form.clarity || ""} onChange={e => set("clarity", e.target.value)} placeholder="VS1" /></div>
                    <div><label>Jeweler/Appraiser</label><input value={form.jeweler || ""} onChange={e => set("jeweler", e.target.value)} placeholder="Tiffany & Co." /></div>
                  </div>
                </div>
              )}
            </div>

            <div style={{ display: "flex", gap: 10, marginTop: 20 }}>
              <button className="btn btn-ghost" style={{ flex: 1 }} onClick={onClose}>Cancel</button>
              <button className="btn btn-primary" style={{ flex: 2 }} onClick={() => { onAdd({ ...form, id: Date.now(), confirmed: false }); onClose(); }}>
                <Icon name="check" size={16} color="#000" /> Confirm & Add Item
              </button>
            </div>
          </div>
        )}
      </div>
    </div>
  );
};

// ─── DASHBOARD ────────────────────────────────────────────────────────────────
const Dashboard = ({ userType, role, clientInfo, onLogout }) => {
  const [page, setPage] = useState("dashboard");
  const [items, setItems] = useState(mockItems);
  const [selectedRoom, setSelectedRoom] = useState(null);
  const [showScan, setShowScan] = useState(false);
  const [editItem, setEditItem] = useState(null);
  const [exportMsg, setExportMsg] = useState(null);

  const isEnterprise = userType === "enterprise";
  const col = isEnterprise ? theme.accentGold : theme.accent;

  // Compute totals
  const allItems = Object.values(items).flat();
  const totalACV = allItems.reduce((s, i) => s + Number(i.acv), 0);
  const totalRCV = allItems.reduce((s, i) => s + Number(i.rcv), 0);
  const rooms = Object.keys(items);

  const addItem = (room, item) => setItems(prev => ({ ...prev, [room]: [...(prev[room] || []), item] }));
  const updateItem = (room, updated) => setItems(prev => ({ ...prev, [room]: prev[room].map(i => i.id === updated.id ? updated : i) }));
  const deleteItem = (room, id) => setItems(prev => ({ ...prev, [room]: prev[room].filter(i => i.id !== id) }));

  const exportExcel = () => {
    setExportMsg("📊 Generating Excel report...");
    setTimeout(() => {
      setExportMsg("✅ Export ready: Home_Inventory_Report.xlsx");
      setTimeout(() => setExportMsg(null), 3000);
    }, 1800);
  };

  const navItems = [
    { id: "dashboard", icon: "home", label: "Dashboard" },
    { id: "rooms", icon: "rooms", label: "Rooms & Items" },
    { id: "reports", icon: "chart", label: "Reports" },
    { id: "export", icon: "export", label: "Export" },
  ];

  return (
    <div style={{ display: "flex", minHeight: "100vh", background: theme.bg }}>
      {showScan && selectedRoom && (
        <ScanModal room={selectedRoom} onClose={() => setShowScan(false)} onAdd={(item) => addItem(selectedRoom, item)} />
      )}

      {/* Sidebar */}
      <div style={{ width: 240, background: theme.bgCard, borderRight: `1px solid ${theme.border}`, display: "flex", flexDirection: "column", padding: "24px 16px" }}>
        <div style={{ display: "flex", alignItems: "center", gap: 10, padding: "0 8px", marginBottom: 28 }}>
          <div style={{ width: 32, height: 32, borderRadius: 8, background: `linear-gradient(135deg, ${col}, ${theme.accentGreen})`, display: "flex", alignItems: "center", justifyContent: "center" }}>
            <Icon name="home" size={14} color="#000" />
          </div>
          <div>
            <div className="serif" style={{ fontSize: 14 }}>InvenTrak <span style={{ color: col }}>AI</span></div>
            <div style={{ fontSize: 10, color: theme.textMuted }}>
              {isEnterprise ? ({ insurance: "Insurance", restoration: "Restoration", appraiser: "Appraiser" }[role]) : "Homeowner"}
            </div>
          </div>
        </div>

        <nav style={{ flex: 1 }}>
          {navItems.map(n => (
            <div key={n.id} className={`nav-item ${page === n.id ? "active" : ""}`} onClick={() => setPage(n.id)}>
              <Icon name={n.icon} size={16} color={page === n.id ? col : theme.textMuted} />
              {n.label}
            </div>
          ))}
        </nav>

        <div style={{ borderTop: `1px solid ${theme.border}`, paddingTop: 16, marginTop: 16 }}>
          {isEnterprise && clientInfo && (
            <div style={{ padding: "10px 14px", background: theme.bgElevated, borderRadius: 8, marginBottom: 12, fontSize: 12 }}>
              <div style={{ color: theme.textMuted, marginBottom: 4 }}>Client</div>
              <div style={{ fontWeight: 600, color: theme.text, marginBottom: 2 }}>{clientInfo.clientName || "—"}</div>
              <div style={{ color: theme.textMuted, fontSize: 11 }}>{clientInfo.clientAddress ? clientInfo.clientAddress.substring(0, 28) + "..." : "—"}</div>
            </div>
          )}
          <div className="nav-item" onClick={onLogout}>
            <Icon name="logout" size={16} color={theme.textMuted} />
            Sign Out
          </div>
        </div>
      </div>

      {/* Main */}
      <div style={{ flex: 1, overflowY: "auto" }}>
        {/* Top bar */}
        <div style={{ padding: "20px 32px", borderBottom: `1px solid ${theme.border}`, display: "flex", justifyContent: "space-between", alignItems: "center", position: "sticky", top: 0, background: theme.bg, zIndex: 10 }}>
          <div>
            <h2 className="serif" style={{ fontSize: 22 }}>
              {page === "dashboard" && "Home Dashboard"}
              {page === "rooms" && "Rooms & Items"}
              {page === "reports" && "Reports"}
              {page === "export" && "Export"}
            </h2>
            {isEnterprise && clientInfo?.clientName && <div style={{ fontSize: 12, color: theme.textMuted, marginTop: 2 }}>Client: {clientInfo.clientName}</div>}
          </div>
          <div style={{ display: "flex", gap: 10 }}>
            {exportMsg && <div style={{ padding: "8px 16px", background: "rgba(0,229,160,0.1)", border: `1px solid rgba(0,229,160,0.3)`, borderRadius: 8, fontSize: 13, color: theme.accentGreen }}>{exportMsg}</div>}
            <button className="btn btn-outline btn-sm" onClick={exportExcel}>
              <Icon name="export" size={14} color={theme.accent} /> Export Excel
            </button>
          </div>
        </div>

        <div style={{ padding: 32 }}>

          {/* ── DASHBOARD PAGE ── */}
          {page === "dashboard" && (
            <div className="fade-in">
              <div className="grid-4" style={{ marginBottom: 28 }}>
                <StatCard label="Total ACV" value={fmt(totalACV)} sub="Actual Cash Value" color={theme.accent} icon={<Icon name="chart" size={20} color={theme.accent} />} />
                <StatCard label="Total RCV" value={fmt(totalRCV)} sub="Replacement Cost Value" color={theme.accentGold} icon={<Icon name="diamond" size={20} color={theme.accentGold} />} />
                <StatCard label="Total Items" value={allItems.length} sub={`${allItems.filter(i => i.confirmed).length} confirmed`} color={theme.accentGreen} icon={<Icon name="check" size={20} color={theme.accentGreen} />} />
                <StatCard label="Rooms" value={rooms.length} sub="Rooms scanned" color={theme.textMuted} icon={<Icon name="rooms" size={20} color={theme.textMuted} />} />
              </div>

              {isEnterprise && clientInfo && (
                <div className="card" style={{ marginBottom: 24, display: "flex", gap: 32, flexWrap: "wrap" }}>
                  <div>
                    <div style={{ fontSize: 11, color: theme.textMuted, textTransform: "uppercase", letterSpacing: "0.1em", marginBottom: 4 }}>Residents</div>
                    <div style={{ fontSize: 18, fontWeight: 600 }}>{clientInfo.numResidents || "—"}</div>
                    {clientInfo.residentAges && <div style={{ fontSize: 12, color: theme.textMuted }}>Ages: {clientInfo.residentAges}</div>}
                  </div>
                  <div>
                    <div style={{ fontSize: 11, color: theme.textMuted, textTransform: "uppercase", letterSpacing: "0.1em", marginBottom: 4 }}>Policy Limit</div>
                    <div style={{ fontSize: 18, fontWeight: 600 }}>{clientInfo.policyLimit ? fmt(clientInfo.policyLimit) : "—"}</div>
                  </div>
                  <div>
                    <div style={{ fontSize: 11, color: theme.textMuted, textTransform: "uppercase", letterSpacing: "0.1em", marginBottom: 4 }}>Bedrooms</div>
                    <div style={{ fontSize: 18, fontWeight: 600 }}>{clientInfo.numBedrooms || "—"} / {clientInfo.numRooms || "—"} rooms</div>
                  </div>
                  {Object.keys(clientInfo.subLimits || {}).filter(k => clientInfo.subLimits[k]).length > 0 && (
                    <div>
                      <div style={{ fontSize: 11, color: theme.textMuted, textTransform: "uppercase", letterSpacing: "0.1em", marginBottom: 4 }}>Sub-Limits Active</div>
                      <div style={{ display: "flex", gap: 6, flexWrap: "wrap" }}>
                        {Object.entries(clientInfo.subLimits || {}).filter(([, v]) => v).map(([k, v]) => (
                          <span key={k} className="stat-badge badge-gold">{k}: {fmt(v)}</span>
                        ))}
                      </div>
                    </div>
                  )}
                </div>
              )}

              <h3 style={{ fontSize: 14, fontWeight: 700, color: theme.textMuted, textTransform: "uppercase", letterSpacing: "0.1em", marginBottom: 16 }}>Room Summaries</h3>
              <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fill, minmax(280px, 1fr))", gap: 16 }}>
                {rooms.map(room => {
                  const roomItems = items[room] || [];
                  const rACV = roomItems.reduce((s, i) => s + Number(i.acv), 0);
                  const rRCV = roomItems.reduce((s, i) => s + Number(i.rcv), 0);
                  return (
                    <div key={room} className="card card-hover" onClick={() => { setSelectedRoom(room); setPage("rooms"); }} style={{ position: "relative" }}>
                      <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 12 }}>
                        <div style={{ fontWeight: 600, fontSize: 15 }}>{room}</div>
                        <span className="stat-badge badge-cyan">{roomItems.length} items</span>
                      </div>
                      <div style={{ display: "flex", gap: 20 }}>
                        <div><div style={{ fontSize: 10, color: theme.textMuted, marginBottom: 2 }}>ACV</div><div className="mono" style={{ fontSize: 16, color: theme.accent }}>{fmt(rACV)}</div></div>
                        <div><div style={{ fontSize: 10, color: theme.textMuted, marginBottom: 2 }}>RCV</div><div className="mono" style={{ fontSize: 16, color: theme.accentGold }}>{fmt(rRCV)}</div></div>
                      </div>
                      <div style={{ marginTop: 12, display: "flex", gap: 4, flexWrap: "wrap" }}>
                        {[...new Set(roomItems.map(i => i.category))].slice(0, 3).map(c => (
                          <span key={c} className="tag" style={{ background: theme.bgElevated, color: theme.textMuted, fontSize: 10 }}>{c}</span>
                        ))}
                      </div>
                    </div>
                  );
                })}
                <div className="card" style={{ border: `2px dashed ${theme.border}`, display: "flex", alignItems: "center", justifyContent: "center", cursor: "pointer", minHeight: 120, flexDirection: "column", gap: 8, color: theme.textMuted }}
                  onClick={() => setPage("rooms")}>
                  <Icon name="plus" size={20} color={theme.textMuted} />
                  <div style={{ fontSize: 13 }}>Add Room</div>
                </div>
              </div>
            </div>
          )}

          {/* ── ROOMS PAGE ── */}
          {page === "rooms" && (
            <div className="fade-in">
              <div style={{ display: "flex", gap: 8, flexWrap: "wrap", marginBottom: 24 }}>
                {rooms.map(r => (
                  <button key={r} className={`btn ${selectedRoom === r ? "btn-primary" : "btn-ghost"} btn-sm`} onClick={() => setSelectedRoom(r)}>
                    {r}
                  </button>
                ))}
                <button className="btn btn-outline btn-sm"><Icon name="plus" size={12} color={theme.accent} /> Add Room</button>
              </div>

              {selectedRoom && (
                <div>
                  <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 20 }}>
                    <div>
                      <h3 className="serif" style={{ fontSize: 24 }}>{selectedRoom}</h3>
                      <div style={{ color: theme.textMuted, fontSize: 13 }}>{(items[selectedRoom] || []).length} items</div>
                    </div>
                    <button className="btn btn-primary btn-sm" onClick={() => setShowScan(true)}>
                      <Icon name="camera" size={14} color="#000" /> Scan Item
                    </button>
                  </div>

                  <div className="card" style={{ padding: 0, overflow: "hidden" }}>
                    <table>
                      <thead>
                        <tr>
                          <th>Item</th>
                          <th>Category</th>
                          <th>ACV</th>
                          <th>RCV</th>
                          <th>Retailer</th>
                          <th>Details</th>
                          <th>Status</th>
                          <th></th>
                        </tr>
                      </thead>
                      <tbody>
                        {(items[selectedRoom] || []).map(item => (
                          <tr key={item.id} className="slide-in">
                            <td>
                              <div style={{ display: "flex", alignItems: "center", gap: 10 }}>
                                <span style={{ fontSize: 20 }}>{item.img}</span>
                                <div>
                                  <div style={{ fontWeight: 500, fontSize: 13 }}>{item.name}</div>
                                  {item.serialNumber && <div style={{ fontSize: 11, color: theme.textMuted }} className="mono">S/N: {item.serialNumber}</div>}
                                  {item.carats && <div style={{ fontSize: 11, color: theme.accentGold }}>{item.carats}ct · {item.clarity}</div>}
                                </div>
                              </div>
                            </td>
                            <td><span className="tag" style={{ background: theme.bgElevated, color: theme.textMuted }}>{item.category}</span></td>
                            <td><span className="mono" style={{ color: theme.accent }}>{fmt(item.acv)}</span></td>
                            <td><span className="mono" style={{ color: theme.accentGold }}>{fmt(item.rcv)}</span></td>
                            <td>
                              <a href={item.retailerUrl} style={{ color: theme.accent, fontSize: 12, textDecoration: "none" }} target="_blank" rel="noreferrer">{item.retailer}</a>
                            </td>
                            <td style={{ fontSize: 11, color: theme.textMuted }}>
                              {item.carats ? `${item.carats}ct, ${item.weight}g` : item.serialNumber ? `S/N on file` : "—"}
                            </td>
                            <td>
                              <span className={`stat-badge ${item.confirmed ? "badge-green" : "badge-red"}`} style={{ fontSize: 10 }}>
                                {item.confirmed ? "✓ Confirmed" : "Pending"}
                              </span>
                            </td>
                            <td>
                              <div style={{ display: "flex", gap: 6 }}>
                                <button className="btn btn-ghost btn-sm" style={{ padding: "4px 8px" }} onClick={() => setEditItem({ room: selectedRoom, item })}>
                                  <Icon name="edit" size={12} />
                                </button>
                                <button className="btn btn-danger btn-sm" style={{ padding: "4px 8px" }} onClick={() => deleteItem(selectedRoom, item.id)}>
                                  <Icon name="x" size={12} />
                                </button>
                              </div>
                            </td>
                          </tr>
                        ))}
                      </tbody>
                    </table>
                    {(items[selectedRoom] || []).length === 0 && (
                      <div style={{ padding: 40, textAlign: "center", color: theme.textMuted }}>
                        <Icon name="camera" size={32} color={theme.border} />
                        <div style={{ marginTop: 12, fontSize: 14 }}>No items scanned yet.</div>
                        <button className="btn btn-primary btn-sm" style={{ marginTop: 16 }} onClick={() => setShowScan(true)}>Scan First Item</button>
                      </div>
                    )}
                  </div>

                  {/* Room totals */}
                  <div style={{ display: "flex", gap: 20, marginTop: 16, justifyContent: "flex-end" }}>
                    <div className="stat-badge badge-cyan">Room ACV: {fmt((items[selectedRoom] || []).reduce((s, i) => s + Number(i.acv), 0))}</div>
                    <div className="stat-badge badge-gold">Room RCV: {fmt((items[selectedRoom] || []).reduce((s, i) => s + Number(i.rcv), 0))}</div>
                  </div>
                </div>
              )}
              {!selectedRoom && (
                <div style={{ textAlign: "center", padding: 60, color: theme.textMuted }}>
                  <Icon name="rooms" size={40} color={theme.border} />
                  <div style={{ marginTop: 16, fontSize: 15 }}>Select a room to view items</div>
                </div>
              )}
            </div>
          )}

          {/* ── REPORTS PAGE ── */}
          {page === "reports" && (
            <div className="fade-in">
              <div className="grid-2" style={{ gap: 24 }}>
                <div className="card">
                  <div style={{ fontWeight: 700, marginBottom: 16, fontSize: 15 }}>Room Value Breakdown</div>
                  {rooms.map(room => {
                    const rItems = items[room] || [];
                    const rACV = rItems.reduce((s, i) => s + Number(i.acv), 0);
                    const pct = totalACV ? Math.round((rACV / totalACV) * 100) : 0;
                    return (
                      <div key={room} style={{ marginBottom: 16 }}>
                        <div style={{ display: "flex", justifyContent: "space-between", fontSize: 13, marginBottom: 6 }}>
                          <span>{room}</span>
                          <span className="mono" style={{ color: theme.accent }}>{fmt(rACV)} <span style={{ color: theme.textMuted, fontSize: 11 }}>({pct}%)</span></span>
                        </div>
                        <div className="progress-bar">
                          <div className="progress-fill" style={{ width: `${pct}%` }} />
                        </div>
                      </div>
                    );
                  })}
                </div>

                <div className="card">
                  <div style={{ fontWeight: 700, marginBottom: 16, fontSize: 15 }}>Category Summary</div>
                  {[...new Set(allItems.map(i => i.category))].map(cat => {
                    const catItems = allItems.filter(i => i.category === cat);
                    const catACV = catItems.reduce((s, i) => s + Number(i.acv), 0);
                    return (
                      <div key={cat} style={{ display: "flex", justifyContent: "space-between", padding: "10px 0", borderBottom: `1px solid ${theme.border}`, fontSize: 13 }}>
                        <span>{cat} <span style={{ color: theme.textMuted }}>({catItems.length})</span></span>
                        <span className="mono" style={{ color: theme.accentGold }}>{fmt(catACV)}</span>
                      </div>
                    );
                  })}
                </div>

                <div className="card" style={{ gridColumn: "1/-1" }}>
                  <div style={{ fontWeight: 700, marginBottom: 16, fontSize: 15 }}>ACV vs RCV Summary</div>
                  <table>
                    <thead>
                      <tr><th>Room</th><th>Items</th><th>ACV Total</th><th>RCV Total</th><th>Depreciation</th></tr>
                    </thead>
                    <tbody>
                      {rooms.map(room => {
                        const rItems = items[room] || [];
                        const rACV = rItems.reduce((s, i) => s + Number(i.acv), 0);
                        const rRCV = rItems.reduce((s, i) => s + Number(i.rcv), 0);
                        const dep = rRCV - rACV;
                        return (
                          <tr key={room}>
                            <td style={{ fontWeight: 500 }}>{room}</td>
                            <td>{rItems.length}</td>
                            <td><span className="mono" style={{ color: theme.accent }}>{fmt(rACV)}</span></td>
                            <td><span className="mono" style={{ color: theme.accentGold }}>{fmt(rRCV)}</span></td>
                            <td><span className="mono" style={{ color: theme.accentRed }}>-{fmt(dep)}</span></td>
                          </tr>
                        );
                      })}
                      <tr style={{ fontWeight: 700, borderTop: `2px solid ${theme.border}` }}>
                        <td>TOTAL</td>
                        <td>{allItems.length}</td>
                        <td><span className="mono" style={{ color: theme.accent }}>{fmt(totalACV)}</span></td>
                        <td><span className="mono" style={{ color: theme.accentGold }}>{fmt(totalRCV)}</span></td>
                        <td><span className="mono" style={{ color: theme.accentRed }}>-{fmt(totalRCV - totalACV)}</span></td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          )}

          {/* ── EXPORT PAGE ── */}
          {page === "export" && (
            <div className="fade-in" style={{ maxWidth: 600 }}>
              <div className="card" style={{ marginBottom: 20 }}>
                <div style={{ display: "flex", gap: 16, alignItems: "flex-start" }}>
                  <div style={{ width: 48, height: 48, borderRadius: 10, background: "rgba(0,229,160,0.1)", display: "flex", alignItems: "center", justifyContent: "center" }}>
                    <Icon name="file" size={22} color={theme.accentGreen} />
                  </div>
                  <div>
                    <div style={{ fontWeight: 700, marginBottom: 4 }}>Excel Export (.xlsx)</div>
                    <div style={{ fontSize: 13, color: theme.textMuted, lineHeight: 1.6 }}>
                      Generates a structured Excel file organized by room, item type, ACV, RCV, and retailer. Suitable for insurance submissions and audits.
                    </div>
                  </div>
                </div>
                <div className="divider" />
                <div style={{ display: "flex", flexWrap: "wrap", gap: 10, marginBottom: 20 }}>
                  {["Room-by-room breakdown", "ACV & RCV columns", "Serial numbers", "Retailer links", "Jewelry details", "Resident info"].map(f => (
                    <span key={f} className="stat-badge badge-green" style={{ fontSize: 11 }}>✓ {f}</span>
                  ))}
                </div>
                <div style={{ background: theme.bgElevated, borderRadius: 8, padding: 16, marginBottom: 20 }}>
                  <div style={{ display: "flex", justifyContent: "space-between", fontSize: 13, marginBottom: 8 }}>
                    <span style={{ color: theme.textMuted }}>Total items</span><span>{allItems.length}</span>
                  </div>
                  <div style={{ display: "flex", justifyContent: "space-between", fontSize: 13, marginBottom: 8 }}>
                    <span style={{ color: theme.textMuted }}>Total ACV</span><span className="mono" style={{ color: theme.accent }}>{fmt(totalACV)}</span>
                  </div>
                  <div style={{ display: "flex", justifyContent: "space-between", fontSize: 13 }}>
                    <span style={{ color: theme.textMuted }}>Total RCV</span><span className="mono" style={{ color: theme.accentGold }}>{fmt(totalRCV)}</span>
                  </div>
                </div>
                <button className="btn btn-primary" style={{ width: "100%" }} onClick={exportExcel}>
                  <Icon name="export" size={16} color="#000" /> Generate & Download Excel Report
                </button>
              </div>

              <div className="card">
                <div style={{ fontWeight: 700, marginBottom: 16 }}>Subscription Tier</div>
                <div style={{ display: "grid", gap: 12 }}>
                  {[
                    { name: "Basic", price: "$9/mo", features: "1 home, 5 rooms, PDF export", active: false },
                    { name: "Pro", price: "$19/mo", features: "Multiple homes, Excel export, pricing APIs", active: true },
                    { name: "Enterprise", price: "$149/mo", features: "200 devices, claims teams, API access", active: isEnterprise },
                  ].map(t => (
                    <div key={t.name} style={{ padding: 16, border: `1px solid ${t.active ? theme.accent : theme.border}`, borderRadius: 10, background: t.active ? "rgba(0,212,255,0.04)" : "transparent", display: "flex", justifyContent: "space-between", alignItems: "center" }}>
                      <div>
                        <div style={{ fontWeight: 600, fontSize: 14 }}>{t.name} {t.active && <span className="tag badge-cyan">Current</span>}</div>
                        <div style={{ fontSize: 12, color: theme.textMuted }}>{t.features}</div>
                      </div>
                      <div className="mono" style={{ fontSize: 15, color: t.active ? theme.accent : theme.textMuted }}>{t.price}</div>
                    </div>
                  ))}
                </div>
              </div>
            </div>
          )}
        </div>
      </div>

      {/* Edit Item Modal */}
      {editItem && (
        <div className="modal-overlay">
          <div className="modal">
            <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 24 }}>
              <div className="serif" style={{ fontSize: 20 }}>Edit Item</div>
              <button onClick={() => setEditItem(null)} style={{ background: "none", border: "none", cursor: "pointer", color: theme.textMuted }}><Icon name="x" size={20} /></button>
            </div>
            <div style={{ display: "flex", flexDirection: "column", gap: 14 }}>
              <div><label>Item Name</label><input value={editItem.item.name} onChange={e => setEditItem(ei => ({ ...ei, item: { ...ei.item, name: e.target.value } }))} /></div>
              <div className="grid-2">
                <div><label>ACV ($)</label><input type="number" value={editItem.item.acv} onChange={e => setEditItem(ei => ({ ...ei, item: { ...ei.item, acv: e.target.value } }))} /></div>
                <div><label>RCV ($)</label><input type="number" value={editItem.item.rcv} onChange={e => setEditItem(ei => ({ ...ei, item: { ...ei.item, rcv: e.target.value } }))} /></div>
              </div>
              <div><label>Serial Number</label><input value={editItem.item.serialNumber || ""} onChange={e => setEditItem(ei => ({ ...ei, item: { ...ei.item, serialNumber: e.target.value } }))} /></div>
              <div style={{ display: "flex", gap: 10, marginTop: 8 }}>
                <button className="btn btn-ghost" style={{ flex: 1 }} onClick={() => setEditItem(null)}>Cancel</button>
                <button className="btn btn-primary" style={{ flex: 2 }} onClick={() => { updateItem(editItem.room, { ...editItem.item, confirmed: true }); setEditItem(null); }}>
                  <Icon name="check" size={16} color="#000" /> Save Changes
                </button>
              </div>
            </div>
          </div>
        </div>
      )}
    </div>
  );
};

// ─── ROOT APP ─────────────────────────────────────────────────────────────────
export default function App() {
  const [screen, setScreen] = useState("landing"); // landing | login | intake | dashboard
  const [userType, setUserType] = useState(null);
  const [role, setRole] = useState(null);
  const [clientInfo, setClientInfo] = useState(null);

  return (
    <>
      <style>{css}</style>
      {screen === "landing" && (
        <LandingPage onSelect={(type) => { setUserType(type); setScreen("login"); }} />
      )}
      {screen === "login" && (
        <LoginPage
          userType={userType}
          onBack={() => setScreen("landing")}
          onLogin={(type, r) => {
            setRole(r);
            if (type === "enterprise") setScreen("intake");
            else setScreen("dashboard");
          }}
        />
      )}
      {screen === "intake" && (
        <EnterpriseIntake role={role} onComplete={(info) => { setClientInfo(info); setScreen("dashboard"); }} />
      )}
      {screen === "dashboard" && (
        <Dashboard userType={userType} role={role} clientInfo={clientInfo} onLogout={() => { setScreen("landing"); setUserType(null); setRole(null); setClientInfo(null); }} />
      )}
    </>
  );
}
