import { useState, useRef, useEffect } from "react";

// ─── DATA ────────────────────────────────────────────────────────────────────

const DESTINATIONS = {
  goa: {
    name: "Goa", tagline: "Sun, Sand & Stories", emoji: "🏖️",
    image: "https://images.unsplash.com/photo-1512343879784-a960bf40e7f2?w=700&q=80",
    tags: ["Party", "Beach", "Peace"], color: "#E8621A",
    transports: [
      { id:"t1", type:"flight", name:"IndiGo 6E-501", depart:"06:00", arrive:"08:10", from:"Mumbai", price:3200, duration:"2h 10m", class:"Economy" },
      { id:"t2", type:"flight", name:"Air India AI-203", depart:"10:30", arrive:"12:45", from:"Mumbai", price:4800, duration:"2h 15m", class:"Business" },
      { id:"t3", type:"flight", name:"SpiceJet SG-101", depart:"15:00", arrive:"17:05", from:"Mumbai", price:2900, duration:"2h 05m", class:"Economy" },
      { id:"t4", type:"train", name:"Mandovi Express", depart:"07:10", arrive:"14:30", from:"Mumbai CST", price:680, duration:"7h 20m", class:"Sleeper" },
      { id:"t5", type:"train", name:"Goa Express", depart:"22:00", arrive:"10:40+1", from:"Dadar", price:1200, duration:"12h 40m", class:"3A" },
    ],
    hotels: [
      { id:"h1", name:"Zostel Goa", area:"Calangute", price:800, rating:4.2, type:"Hostel", amenities:["WiFi","Pool","Beach Access"], image:"https://images.unsplash.com/photo-1618773928121-c32242e63f39?w=400&q=70" },
      { id:"h2", name:"Taj Holiday Village", area:"Candolim", price:12000, rating:4.8, type:"Luxury", amenities:["Spa","Pool","Beachfront"], image:"https://images.unsplash.com/photo-1540541338287-41700207dee6?w=400&q=70" },
      { id:"h3", name:"Purple Nest Hostel", area:"Baga", price:1200, rating:4.0, type:"Hostel", amenities:["WiFi","Rooftop","Bar"], image:"https://images.unsplash.com/photo-1555854877-bab0e564b8d5?w=400&q=70" },
      { id:"h4", name:"Acron Waterfront Resort", area:"Baga", price:5500, rating:4.5, type:"Resort", amenities:["Pool","Restaurant","Parking"], image:"https://images.unsplash.com/photo-1602002418082-a4443e081dd1?w=400&q=70" },
    ],
    itinerary:["Beach hopping at Baga & Anjuna","Sunset cruise at Chapora Fort","Spice plantation tour","Flea market at Anjuna","Water sports at Calangute"],
  },
  manali: {
    name:"Manali", tagline:"Mountains & Mystic Snow", emoji:"🏔️",
    image:"https://images.unsplash.com/photo-1506905925346-21bda4d32df4?w=700&q=80",
    tags:["Peace","Nature","Adventure"], color:"#1A6B6B",
    transports:[
      { id:"t1", type:"flight", name:"Air India AI-445", depart:"07:00", arrive:"08:30", from:"Delhi to Bhuntar", price:5200, duration:"1h 30m", class:"Economy" },
      { id:"t2", type:"flight", name:"IndiGo 6E-702", depart:"11:00", arrive:"12:20", from:"Delhi to Bhuntar", price:4600, duration:"1h 20m", class:"Economy" },
      { id:"t3", type:"train", name:"Himalayan Queen", depart:"06:00", arrive:"10:30", from:"Delhi to Kalka", price:560, duration:"4h 30m", class:"CC" },
      { id:"t4", type:"train", name:"Shatabdi Express", depart:"07:20", arrive:"11:00", from:"Delhi to Chandigarh", price:980, duration:"3h 40m", class:"CC" },
      { id:"t5", type:"bus", name:"HRTC Volvo Deluxe", depart:"18:00", arrive:"09:00+1", from:"Delhi ISBT", price:1200, duration:"15h", class:"AC Sleeper" },
    ],
    hotels:[
      { id:"h1", name:"The Himalayan", area:"Old Manali", price:8500, rating:4.7, type:"Boutique", amenities:["Mountain View","Fireplace","Restaurant"], image:"https://images.unsplash.com/photo-1571896349842-33c89424de2d?w=400&q=70" },
      { id:"h2", name:"Snow Valley Resorts", area:"Manali", price:4200, rating:4.3, type:"Resort", amenities:["Garden","Parking","Bonfire"], image:"https://images.unsplash.com/photo-1542314831-068cd1dbfeeb?w=400&q=70" },
      { id:"h3", name:"Zostel Manali", area:"Old Manali", price:700, rating:4.1, type:"Hostel", amenities:["WiFi","Common Area","Trekking"], image:"https://images.unsplash.com/photo-1520250497591-112f2f40a3f4?w=400&q=70" },
      { id:"h4", name:"Hotel Beas Residency", area:"Mall Road", price:2800, rating:4.0, type:"Hotel", amenities:["Restaurant","Heating","Parking"], image:"https://images.unsplash.com/photo-1564501049412-61c2a3083791?w=400&q=70" },
    ],
    itinerary:["Rohtang Pass snowfields","Old Manali cafe hopping","Solang Valley adventure","Hadimba Temple visit","River rafting on Beas"],
  },
  mumbai: {
    name:"Mumbai", tagline:"The City That Never Sleeps", emoji:"🌆",
    image:"https://images.unsplash.com/photo-1529253355930-ddbe423a2ac7?w=700&q=80",
    tags:["Chaos","City Life","Hi-Fi"], color:"#C8922A",
    transports:[
      { id:"t1", type:"flight", name:"IndiGo 6E-210", depart:"06:30", arrive:"08:00", from:"Delhi", price:3800, duration:"1h 30m", class:"Economy" },
      { id:"t2", type:"flight", name:"Vistara UK-901", depart:"10:00", arrive:"11:40", from:"Delhi", price:6200, duration:"1h 40m", class:"Business" },
      { id:"t3", type:"train", name:"Rajdhani Express", depart:"16:25", arrive:"08:15+1", from:"New Delhi", price:1850, duration:"15h 50m", class:"2A" },
      { id:"t4", type:"train", name:"August Kranti Express", depart:"17:40", arrive:"10:55+1", from:"Hazrat Nizamuddin", price:1200, duration:"17h 15m", class:"3A" },
      { id:"t5", type:"flight", name:"Air India AI-111", depart:"20:00", arrive:"21:35", from:"Delhi", price:4100, duration:"1h 35m", class:"Economy" },
    ],
    hotels:[
      { id:"h1", name:"The Taj Mahal Palace", area:"Colaba", price:22000, rating:4.9, type:"Luxury", amenities:["Sea View","Spa","Multiple Restaurants"], image:"https://images.unsplash.com/photo-1566073771259-6a8506099945?w=400&q=70" },
      { id:"h2", name:"Abode Bombay", area:"Colaba", price:7500, rating:4.5, type:"Boutique", amenities:["Heritage Building","WiFi","Breakfast"], image:"https://images.unsplash.com/photo-1551882547-ff40c63fe5fa?w=400&q=70" },
      { id:"h3", name:"Hotel Kohinoor Continental", area:"Andheri", price:4200, rating:4.0, type:"Hotel", amenities:["Airport Shuttle","Restaurant","Gym"], image:"https://images.unsplash.com/photo-1578683010236-d716f9a3f461?w=400&q=70" },
      { id:"h4", name:"Backpacker Panda", area:"Bandra", price:900, rating:4.3, type:"Hostel", amenities:["WiFi","Social Hub","Bar"], image:"https://images.unsplash.com/photo-1555854877-bab0e564b8d5?w=400&q=70" },
    ],
    itinerary:["Gateway of India & waterfront","Dharavi tour experience","Marine Drive sunset walk","Crawford Market shopping","Bollywood studio tour"],
  },
  jaipur: {
    name:"Jaipur", tagline:"The Pink City of Royals", emoji:"🏰",
    image:"https://images.unsplash.com/photo-1599661046289-e31897846e41?w=700&q=80",
    tags:["Culture","Heritage","Royal"], color:"#B5447A",
    transports:[
      { id:"t1", type:"flight", name:"IndiGo 6E-608", depart:"07:30", arrive:"08:45", from:"Mumbai", price:4100, duration:"1h 15m", class:"Economy" },
      { id:"t2", type:"flight", name:"Air India AI-541", depart:"13:00", arrive:"14:20", from:"Mumbai", price:5600, duration:"1h 20m", class:"Business" },
      { id:"t3", type:"train", name:"Pink City Express", depart:"05:55", arrive:"13:00", from:"New Delhi", price:680, duration:"7h 05m", class:"Sleeper" },
      { id:"t4", type:"train", name:"Ajmer Shatabdi", depart:"06:05", arrive:"10:40", from:"New Delhi", price:1120, duration:"4h 35m", class:"CC" },
      { id:"t5", type:"train", name:"Double Decker Express", depart:"15:55", arrive:"21:05", from:"New Delhi", price:760, duration:"5h 10m", class:"CC" },
    ],
    hotels:[
      { id:"h1", name:"Rambagh Palace", area:"Bhawani Singh Rd", price:28000, rating:5.0, type:"Palace Hotel", amenities:["Heritage","Polo Ground","Spa"], image:"https://images.unsplash.com/photo-1603199506016-b9a594b593c0?w=400&q=70" },
      { id:"h2", name:"Dera Mandawa", area:"Sansar Chandra Rd", price:6800, rating:4.6, type:"Heritage", amenities:["Rooftop Pool","Rajasthani Decor","Tours"], image:"https://images.unsplash.com/photo-1561501900-3701fa6a0864?w=400&q=70" },
      { id:"h3", name:"Hotel Pearl Palace", area:"Hathroi Fort", price:1800, rating:4.4, type:"Budget", amenities:["Rooftop Cafe","Tour Desk","WiFi"], image:"https://images.unsplash.com/photo-1520250497591-112f2f40a3f4?w=400&q=70" },
      { id:"h4", name:"Zostel Jaipur", area:"Bani Park", price:650, rating:4.1, type:"Hostel", amenities:["Social Events","WiFi","Kitchen"], image:"https://images.unsplash.com/photo-1555854877-bab0e564b8d5?w=400&q=70" },
    ],
    itinerary:["Amber Fort & elephant safari","City Palace & Jantar Mantar","Hawa Mahal photo walk","Johari Bazaar gem shopping","Local thali & folk performance"],
  },
  bangalore: {
    name:"Bangalore", tagline:"Silicon Valley Meets Chill", emoji:"🌿",
    image:"https://images.unsplash.com/photo-1596176530529-78163a4f7af2?w=700&q=80",
    tags:["Modern","Chill","Urban"], color:"#2A7A4A",
    transports:[
      { id:"t1", type:"flight", name:"IndiGo 6E-301", depart:"06:00", arrive:"07:30", from:"Mumbai", price:3500, duration:"1h 30m", class:"Economy" },
      { id:"t2", type:"flight", name:"Vistara UK-821", depart:"09:30", arrive:"11:00", from:"Mumbai", price:5800, duration:"1h 30m", class:"Business" },
      { id:"t3", type:"train", name:"UBL Rajdhani", depart:"20:00", arrive:"10:30+1", from:"Hazrat Nizamuddin", price:2100, duration:"14h 30m", class:"2A" },
      { id:"t4", type:"train", name:"Karnataka Express", depart:"20:20", arrive:"07:45+2", from:"New Delhi", price:1450, duration:"35h 25m", class:"3A" },
      { id:"t5", type:"flight", name:"Air Asia I5-501", depart:"18:00", arrive:"19:25", from:"Mumbai", price:2800, duration:"1h 25m", class:"Economy" },
    ],
    hotels:[
      { id:"h1", name:"The Leela Palace", area:"HAL Airport Rd", price:18000, rating:4.9, type:"Luxury", amenities:["Pool","Spa","Multiple Outlets"], image:"https://images.unsplash.com/photo-1566073771259-6a8506099945?w=400&q=70" },
      { id:"h2", name:"ITC Gardenia", area:"Residency Road", price:9500, rating:4.7, type:"Business Luxury", amenities:["Rooftop Pool","Yoga","Restaurant"], image:"https://images.unsplash.com/photo-1571896349842-33c89424de2d?w=400&q=70" },
      { id:"h3", name:"Treehouse Serviced Apt", area:"Koramangala", price:3200, rating:4.4, type:"Apartment", amenities:["Kitchen","WiFi","Workspace"], image:"https://images.unsplash.com/photo-1618773928121-c32242e63f39?w=400&q=70" },
      { id:"h4", name:"Zostel Bangalore", area:"Indiranagar", price:750, rating:4.2, type:"Hostel", amenities:["Social Events","Bar","Terrace"], image:"https://images.unsplash.com/photo-1555854877-bab0e564b8d5?w=400&q=70" },
    ],
    itinerary:["Lalbagh Botanical Garden morning","Cubbon Park cycling","Brewery & craft beer trail","Koramangala food walk","ISKCON temple & gardens"],
  },
};

const PERSONALITY_QUESTIONS = [
  { id:"q1", question:"What is your ideal vacation vibe?", icon:"✨",
    options:[
      { label:"Party & dance all night", value:"party", icon:"🎉" },
      { label:"Peace and quiet with nature", value:"peace", icon:"🌿" },
      { label:"Explore chaos & city rush", value:"chaos", icon:"🌆" },
      { label:"Learn history & culture", value:"culture", icon:"🏛️" },
    ]},
  { id:"q2", question:"What energises you most on a trip?", icon:"⚡",
    options:[
      { label:"Beaches, sunsets, ocean breeze", value:"beach", icon:"🏖️" },
      { label:"Mountains, snow, fresh air", value:"mountains", icon:"🏔️" },
      { label:"Street food, markets, nightlife", value:"urban", icon:"🍜" },
      { label:"Palaces, museums, art galleries", value:"heritage", icon:"🎨" },
    ]},
  { id:"q3", question:"Your travel style is best described as...", icon:"🎒",
    options:[
      { label:"Budget backpacker, max experience", value:"budget", icon:"🎒" },
      { label:"Comfortable mid-range", value:"midrange", icon:"🏨" },
      { label:"Luxury, no compromise", value:"luxury", icon:"💎" },
      { label:"Digital nomad / work & travel", value:"nomad", icon:"💻" },
    ]},
];

function matchDestination(answers) {
  const [vibe, energy] = answers;
  if (vibe==="party"||energy==="beach") return "goa";
  if (vibe==="peace"||energy==="mountains") return "manali";
  if (vibe==="chaos"||energy==="urban") return "mumbai";
  if (vibe==="culture"||energy==="heritage") return "jaipur";
  return "bangalore";
}

function ratePlan(transport, hotel, budget, days) {
  if (!transport||!hotel||!budget) return null;
  const total = transport.price + hotel.price * days;
  const ratio = total / budget;
  if (ratio>1.3) return { stars:1, label:"Over Budget", color:"#ef4444", tip:`Your plan exceeds budget by ${Math.round((ratio-1)*100)}%. Consider cheaper options.` };
  if (ratio>1.0) return { stars:2, label:"Slightly Over", color:"#f97316", tip:`Just ${Math.round((ratio-1)*100)}% over budget. Small adjustments needed.` };
  if (ratio>0.85) return { stars:3, label:"Good Plan", color:"#eab308", tip:"Well within budget! Good mobility balance." };
  if (ratio>0.65) return { stars:4, label:"Efficient", color:"#22c55e", tip:"Excellent cost-to-comfort ratio! Smart planning." };
  return { stars:5, label:"Optimal", color:"#10b981", tip:"Perfect! Highly efficient and budget-smart." };
}

function getAIOptimizedPlan(destKey, budget, days) {
  const dest = DESTINATIONS[destKey];
  const sorted_t = [...dest.transports].sort((a,b)=>a.price-b.price);
  const sorted_h = [...dest.hotels].sort((a,b)=>a.price-b.price);
  let bestT = sorted_t[0], bestH = sorted_h[0];
  for (const t of sorted_t) for (const h of sorted_h) {
    if (t.price + h.price*days <= budget*0.92) {
      if (h.rating>bestH.rating||(h.rating===bestH.rating&&h.price<bestH.price)) { bestT=t; bestH=h; }
    }
  }
  return { transport:bestT, hotel:bestH };
}

// ─── CSS ─────────────────────────────────────────────────────────────────────
const G = `
@import url('https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,400;0,600;0,700;1,500;1,700&family=DM+Sans:wght@300;400;500;600&display=swap');
*{box-sizing:border-box;margin:0;padding:0;}
:root{
  --saffron:#E8621A;--saffron2:#F28C3A;--gold:#C8922A;
  --ivory:#FAF6EF;--cream:#F5EDD8;--deep:#1C1208;
  --ink:#2C1A0E;--brown:#6B4C2A;--border:rgba(200,146,42,0.18);
  --peacock:#1A6B6B;--terracotta:#C45C30;
}
body{font-family:'DM Sans',sans-serif;background:var(--ivory);color:var(--ink);}
@keyframes fadeUp{from{opacity:0;transform:translateY(22px)}to{opacity:1;transform:translateY(0)}}
@keyframes slideIn{from{opacity:0;transform:translateX(18px)}to{opacity:1;transform:translateX(0)}}
@keyframes pulse{0%,100%{opacity:1}50%{opacity:.4}}
@keyframes spin{from{transform:rotate(0)}to{transform:rotate(360deg)}}
@keyframes chatPop{from{opacity:0;transform:scale(.85) translateY(12px)}to{opacity:1;transform:scale(1) translateY(0)}}
.fade-up{animation:fadeUp .65s ease forwards}
.slide-in{animation:slideIn .4s ease forwards}
input:focus,textarea:focus{outline:none;border-color:var(--saffron)!important;box-shadow:0 0 0 3px rgba(232,98,26,.12)!important;}
::placeholder{color:rgba(74,44,10,.35)!important;}
::-webkit-scrollbar{width:4px}::-webkit-scrollbar-thumb{background:rgba(200,146,42,.3);border-radius:4px}
`;

// ─── SHARED STYLES ────────────────────────────────────────────────────────────
const S = {
  page:{ background:"linear-gradient(145deg,#FAF6EF 0%,#F5EDD8 100%)", minHeight:"100vh" },
  glass:{ background:"rgba(255,255,255,0.72)", backdropFilter:"blur(16px)", border:"1px solid rgba(200,146,42,0.18)", borderRadius:"20px" },
  btn:{ background:"linear-gradient(135deg,#E8621A,#C45C30)", color:"#fff", border:"none", padding:"13px 32px", borderRadius:"50px", fontSize:"14px", fontFamily:"'DM Sans',sans-serif", fontWeight:600, cursor:"pointer", transition:"all .3s", letterSpacing:".3px" },
  btnOut:{ background:"transparent", color:"var(--ink)", border:"1.5px solid rgba(74,44,10,.22)", padding:"12px 26px", borderRadius:"50px", fontSize:"14px", fontFamily:"'DM Sans',sans-serif", cursor:"pointer", transition:"all .3s" },
  inp:{ width:"100%", background:"rgba(255,255,255,.8)", border:"1.5px solid rgba(200,146,42,.25)", color:"var(--ink)", padding:"12px 16px", borderRadius:"12px", fontSize:"15px", fontFamily:"'DM Sans',sans-serif" },
  label:{ fontSize:"11px", color:"var(--brown)", letterSpacing:"1.5px", textTransform:"uppercase", marginBottom:"6px", display:"block", fontWeight:500 },
  card:{ background:"#fff", border:"1px solid rgba(200,146,42,.18)", borderRadius:"16px", overflow:"hidden", transition:"all .35s cubic-bezier(.34,1.56,.64,1)", cursor:"pointer" },
  tag:(c)=>({ background:c+"22", color:c, padding:"3px 10px", borderRadius:"20px", fontSize:"11px", fontWeight:600, letterSpacing:".5px" }),
  sectionLabel:{ fontSize:"11px", letterSpacing:"3px", textTransform:"uppercase", color:"var(--saffron)", marginBottom:"8px", fontWeight:500 },
  h2:{ fontFamily:"'Cormorant Garamond',serif", fontSize:"clamp(28px,3vw,40px)", fontWeight:700, color:"var(--deep)", marginBottom:"8px" },
};

// ─── APP ──────────────────────────────────────────────────────────────────────
export default function App() {
  const [screen, setScreen] = useState("landing");
  const [user, setUser] = useState({ name:"", email:"", phone:"" });
  const [loginErrors, setLoginErrors] = useState({});
  const [quizStep, setQuizStep] = useState(0);
  const [quizAnswers, setQuizAnswers] = useState([]);
  const [suggestedDest, setSuggestedDest] = useState(null);
  const [chosenDest, setChosenDest] = useState(null);
  const [planMode, setPlanMode] = useState(null);
  // onboarding
  const [days, setDays] = useState(3);
  const [budget, setBudget] = useState("");
  const [selectedTransport, setSelectedTransport] = useState(null);
  const [selectedHotel, setSelectedHotel] = useState(null);
  const [transportFilter, setTransportFilter] = useState("all");
  // ai
  const [aiResult, setAiResult] = useState(null);
  const [aiStream, setAiStream] = useState("");
  const [aiLoading, setAiLoading] = useState(false);
  const [manualResult, setManualResult] = useState(null);
  // chat
  const [chatOpen, setChatOpen] = useState(false);
  const [chatInput, setChatInput] = useState("");
  const [chatHistory, setChatHistory] = useState([
    { role:"assistant", text:"Hi! I'm your ChaloGhoomne travel assistant. Ask me anything about destinations, budgets, or itineraries!" }
  ]);
  const [chatLoading, setChatLoading] = useState(false);
  const chatEndRef = useRef(null);

  const dest = chosenDest ? DESTINATIONS[chosenDest] : null;
  const totalCost = (t,h) => t&&h ? t.price + h.price*days : 0;

  useEffect(()=>{ chatEndRef.current?.scrollIntoView({behavior:"smooth"}); },[chatHistory,chatLoading]);

  const validateLogin = () => {
    const e = {};
    if (!user.name.trim()) e.name="Name is required";
    if (!user.email.includes("@")) e.email="Valid email required";
    if (user.phone.replace(/\D/g,"").length<10) e.phone="Valid 10-digit number required";
    setLoginErrors(e);
    return !Object.keys(e).length;
  };

  const handleQuizAnswer = (val) => {
    const ans = [...quizAnswers, val];
    if (quizStep < PERSONALITY_QUESTIONS.length-1) { setQuizAnswers(ans); setQuizStep(quizStep+1); }
    else { setSuggestedDest(matchDestination(ans)); setQuizAnswers(ans); setScreen("suggestion"); }
  };

  const handleAIGenerate = async () => {
    setAiLoading(true); setAiStream(""); setAiResult(null);
    const optimized = getAIOptimizedPlan(chosenDest, parseFloat(budget), days);
    const d = DESTINATIONS[chosenDest];
    const prompt = `You are a premium Indian travel planner AI. Generate an exciting ${days}-day travel itinerary for ${d.name}.
Plan details:
- Transport: ${optimized.transport.name} (₹${optimized.transport.price}) — ${optimized.transport.type}
- Hotel: ${optimized.hotel.name} in ${optimized.hotel.area} (₹${optimized.hotel.price}/night × ${days} nights)
- Total Budget: ₹${budget}
- Trip duration: ${days} days

Write a warm, specific ${days}-day itinerary (max 200 words). Morning/Afternoon/Evening for each day. Start with "Day 1:". Be specific to ${d.name}'s real attractions. Keep language energetic and inspiring. No Hindi words.`;
    try {
      const res = await fetch("https://api.anthropic.com/v1/messages",{
        method:"POST",
        headers:{"Content-Type":"application/json"},
        body:JSON.stringify({ model:"claude-sonnet-4-20250514", max_tokens:1000, stream:true, messages:[{role:"user",content:prompt}] })
      });
      const reader = res.body.getReader(); const dec = new TextDecoder(); let text="";
      while(true){
        const {done,value} = await reader.read(); if(done) break;
        for (const line of dec.decode(value).split("\n")) {
          if (line.startsWith("data: ")) { try{ const d=JSON.parse(line.slice(6)); if(d.type==="content_block_delta"&&d.delta?.text){text+=d.delta.text;setAiStream(text);} }catch{} }
        }
      }
      setAiResult(optimized);
    } catch(e) {
      setAiStream(`Day 1: Arrive and check in at ${optimized.hotel.name}. Explore the local area in the evening.\nDay 2: Full day sightseeing at key attractions.\nDay ${days}: Morning leisure, checkout and depart.`);
      setAiResult(optimized);
    }
    setAiLoading(false);
  };

  const sendChat = async () => {
    if (!chatInput.trim()) return;
    const userMsg = chatInput.trim(); setChatInput("");
    const newHistory = [...chatHistory, {role:"user",text:userMsg}];
    setChatHistory(newHistory); setChatLoading(true);
    const context = chosenDest ? `User is planning a trip to ${DESTINATIONS[chosenDest].name}.` : "User is exploring Indian destinations.";
    const prompt = `You are ChaloGhoomne's friendly travel assistant for India. ${context} Answer helpfully and concisely. No markdown, just plain text. User: ${userMsg}`;
    try {
      const res = await fetch("https://api.anthropic.com/v1/messages",{
        method:"POST",
        headers:{"Content-Type":"application/json"},
        body:JSON.stringify({ model:"claude-sonnet-4-20250514", max_tokens:300, messages:[{role:"user",content:prompt}] })
      });
      const data = await res.json();
      const reply = data.content?.map(i=>i.text||"").join("") || "I'm here to help with your travel plans!";
      setChatHistory([...newHistory,{role:"assistant",text:reply}]);
    } catch { setChatHistory([...newHistory,{role:"assistant",text:"I'm having trouble connecting right now. Please try again!"}]); }
    setChatLoading(false);
  };

  // ── SCREENS ──

  if (screen==="landing") return (
    <div style={S.page}>
      <style>{G}</style>
      {/* NAV */}
      <nav style={{ position:"sticky",top:0,zIndex:100,display:"flex",justifyContent:"space-between",alignItems:"center",padding:"16px 48px",background:"rgba(250,246,239,.92)",backdropFilter:"blur(16px)",borderBottom:"1px solid var(--border)" }}>
        <div style={{ display:"flex",alignItems:"center",gap:10 }}>
          <div style={{ width:38,height:38,background:"linear-gradient(135deg,#E8621A,#C8922A)",borderRadius:10,display:"flex",alignItems:"center",justifyContent:"center",fontSize:18 }}>🪔</div>
          <div>
            <div style={{ fontFamily:"'Cormorant Garamond',serif",fontSize:20,fontWeight:700,color:"var(--deep)" }}>ChaloGhoomne<span style={{color:"var(--saffron)"}}>.in</span></div>
            <div style={{ fontSize:10,color:"var(--saffron)",letterSpacing:2,textTransform:"uppercase",marginTop:-2,fontWeight:500 }}>AI Travel Intelligence</div>
          </div>
        </div>
        <button style={S.btn} onMouseEnter={e=>{e.target.style.transform="translateY(-2px)";e.target.style.boxShadow="0 10px 28px rgba(232,98,26,.35)"}} onMouseLeave={e=>{e.target.style.transform="";e.target.style.boxShadow=""}} onClick={()=>setScreen("login")}>Begin Your Journey →</button>
      </nav>

      {/* HERO */}
      <div style={{ maxWidth:1140,margin:"0 auto",padding:"80px 48px 60px",display:"grid",gridTemplateColumns:"1fr 1fr",gap:60,alignItems:"center" }} className="fade-up">
        <div>
          <div style={{ display:"inline-flex",alignItems:"center",gap:8,background:"rgba(232,98,26,.1)",border:"1px solid rgba(232,98,26,.25)",borderRadius:50,padding:"6px 18px",fontSize:12,letterSpacing:1.5,color:"var(--saffron)",textTransform:"uppercase",marginBottom:24,fontWeight:500 }}>🇮🇳 Made for Indian Travellers</div>
          <h1 style={{ fontFamily:"'Cormorant Garamond',serif",fontSize:"clamp(48px,5vw,74px)",fontWeight:700,lineHeight:1.05,color:"var(--deep)",marginBottom:20 }}>
            Plan Smarter.<br/><em style={{fontStyle:"italic",color:"var(--saffron)"}}>Travel Better.</em>
          </h1>
          <p style={{ fontSize:16,color:"var(--brown)",lineHeight:1.85,marginBottom:36,fontWeight:300,maxWidth:440 }}>
            Discover your travel personality. Compare your plan against AI-optimised alternatives. Make every journey through India unforgettable.
          </p>
          <div style={{ display:"flex",gap:14,flexWrap:"wrap" }}>
            <button style={{...S.btn,padding:"15px 38px",fontSize:15}} onMouseEnter={e=>{e.target.style.transform="translateY(-3px)";e.target.style.boxShadow="0 12px 36px rgba(232,98,26,.35)"}} onMouseLeave={e=>{e.target.style.transform="";e.target.style.boxShadow=""}} onClick={()=>setScreen("login")}>Start Planning Free</button>
            <button style={S.btnOut} onMouseEnter={e=>e.target.style.background="var(--cream)"} onMouseLeave={e=>e.target.style.background="transparent"}>How It Works</button>
          </div>
          <div style={{ display:"flex",gap:32,marginTop:40 }}>
            {[["5","Destinations","🗺️"],["2×","Trip Efficiency","⚡"],["AI","Optimised","🤖"]].map(([n,l,ic])=>(
              <div key={n} style={{ textAlign:"center" }}>
                <div style={{ fontFamily:"'Cormorant Garamond',serif",fontSize:30,fontWeight:700,color:"var(--saffron)" }}>{ic} {n}</div>
                <div style={{ fontSize:12,color:"var(--brown)",marginTop:2 }}>{l}</div>
              </div>
            ))}
          </div>
        </div>
        {/* Destination cards preview */}
        <div style={{ display:"grid",gridTemplateColumns:"1fr 1fr",gap:12 }}>
          {Object.entries(DESTINATIONS).slice(0,4).map(([k,d])=>(
            <div key={k} style={{ position:"relative",borderRadius:18,overflow:"hidden",height:180,cursor:"pointer",transition:"all .4s cubic-bezier(.34,1.56,.64,1)",boxShadow:"0 4px 20px rgba(74,44,10,.1)" }}
              onMouseEnter={e=>{e.currentTarget.style.transform="translateY(-8px) scale(1.03)";e.currentTarget.style.boxShadow="0 20px 50px rgba(74,44,10,.2)"}}
              onMouseLeave={e=>{e.currentTarget.style.transform="";e.currentTarget.style.boxShadow="0 4px 20px rgba(74,44,10,.1)"}}
              onClick={()=>setScreen("login")}>
              <img src={d.image} alt={d.name} style={{width:"100%",height:"100%",objectFit:"cover",filter:"brightness(.8) saturate(1.1)"}}/>
              <div style={{position:"absolute",inset:0,background:"linear-gradient(to top,rgba(28,12,8,.85) 0%,transparent 60%)"}}/>
              <div style={{position:"absolute",bottom:14,left:14}}>
                <div style={{fontSize:18,marginBottom:3}}>{d.emoji}</div>
                <div style={{fontFamily:"'Cormorant Garamond',serif",fontSize:18,fontWeight:700,color:"#fff"}}>{d.name}</div>
                <div style={{fontSize:11,color:"rgba(255,255,255,.65)"}}>{d.tagline}</div>
              </div>
            </div>
          ))}
        </div>
      </div>

      {/* DESTINATIONS STRIP */}
      <div style={{ maxWidth:1140,margin:"0 auto",padding:"20px 48px 80px" }}>
        <div style={S.sectionLabel}>5 Curated Destinations</div>
        <div style={{ display:"grid",gridTemplateColumns:"repeat(5,1fr)",gap:14,marginTop:16 }}>
          {Object.entries(DESTINATIONS).map(([k,d])=>(
            <div key={k} style={{ position:"relative",borderRadius:20,overflow:"hidden",height:260,cursor:"pointer",boxShadow:"0 4px 16px rgba(74,44,10,.09)",transition:"all .45s cubic-bezier(.34,1.56,.64,1)" }}
              onMouseEnter={e=>{
                e.currentTarget.style.transform="translateY(-10px) scale(1.04)";
                e.currentTarget.style.boxShadow="0 24px 56px rgba(74,44,10,.22)";
                e.currentTarget.style.zIndex=10;
                const tags = e.currentTarget.querySelector('.dest-tags');
                const arrow = e.currentTarget.querySelector('.dest-arrow');
                if(tags){tags.style.opacity=1;tags.style.transform="translateY(0)";}
                if(arrow){arrow.style.opacity=1;arrow.style.transform="translateY(0)";}
                const img = e.currentTarget.querySelector('img');
                if(img){img.style.transform="scale(1.1)";img.style.filter="brightness(.7) saturate(1.2)";}
              }}
              onMouseLeave={e=>{
                e.currentTarget.style.transform="";
                e.currentTarget.style.boxShadow="0 4px 16px rgba(74,44,10,.09)";
                e.currentTarget.style.zIndex="";
                const tags = e.currentTarget.querySelector('.dest-tags');
                const arrow = e.currentTarget.querySelector('.dest-arrow');
                if(tags){tags.style.opacity=0;tags.style.transform="translateY(6px)";}
                if(arrow){arrow.style.opacity=0;arrow.style.transform="translateY(-6px)";}
                const img = e.currentTarget.querySelector('img');
                if(img){img.style.transform="scale(1)";img.style.filter="brightness(.82) saturate(1.1)";}
              }}
              onClick={()=>setScreen("login")}>
              <img src={d.image} alt={d.name} style={{width:"100%",height:"100%",objectFit:"cover",filter:"brightness(.82) saturate(1.1)",transition:"all .55s ease"}}/>
              <div style={{position:"absolute",inset:0,background:"linear-gradient(to top,rgba(28,12,8,.88) 0%,rgba(28,12,8,.2) 55%,transparent 100%)"}}/>
              <div className="dest-arrow" style={{position:"absolute",top:14,right:14,width:30,height:30,background:"rgba(255,255,255,.18)",backdropFilter:"blur(8px)",border:"1px solid rgba(255,255,255,.25)",borderRadius:"50%",display:"flex",alignItems:"center",justifyContent:"center",color:"#fff",fontSize:13,opacity:0,transform:"translateY(-6px)",transition:"all .3s ease"}}>↗</div>
              <div style={{position:"absolute",bottom:0,left:0,right:0,padding:"18px 16px"}}>
                <div style={{fontSize:22,marginBottom:6}}>{d.emoji}</div>
                <div style={{fontFamily:"'Cormorant Garamond',serif",fontSize:19,fontWeight:700,color:"#fff",lineHeight:1}}>{d.name}</div>
                <div style={{fontSize:11,color:"rgba(255,255,255,.6)",marginTop:3}}>{d.tagline}</div>
                <div className="dest-tags" style={{display:"flex",gap:4,flexWrap:"wrap",marginTop:8,opacity:0,transform:"translateY(6px)",transition:"all .3s ease .05s"}}>
                  {d.tags.map(t=><span key={t} style={{fontSize:9,fontWeight:600,padding:"3px 8px",borderRadius:20,letterSpacing:.5,textTransform:"uppercase",background:"rgba(255,255,255,.18)",color:"rgba(255,255,255,.9)",border:"1px solid rgba(255,255,255,.2)",backdropFilter:"blur(4px)"}}>{t}</span>)}
                </div>
              </div>
            </div>
          ))}
        </div>
      </div>

      {/* FEATURES */}
      <div style={{ background:"var(--deep)",padding:"56px 48px" }}>
        <div style={{ maxWidth:1140,margin:"0 auto",display:"grid",gridTemplateColumns:"repeat(3,1fr)",gap:1,background:"rgba(255,255,255,.06)",borderRadius:20,overflow:"hidden",border:"1px solid rgba(200,146,42,.2)" }}>
          {[["🧠","AI Powered","Intelligent trip optimisation for every budget"],["⭐","Travel Personality","Know yourself before you go. Our quiz finds your perfect match."],["📊","Plan Comparison","Rate your manual plan vs AI. Learn to decide better."]].map(([ic,n,d])=>(
            <div key={n} style={{ padding:"40px 32px",background:"rgba(28,12,8,.95)",transition:"background .3s" }} onMouseEnter={e=>e.currentTarget.style.background="rgba(232,98,26,.08)"} onMouseLeave={e=>e.currentTarget.style.background="rgba(28,12,8,.95)"}>
              <div style={{ fontSize:32,marginBottom:16 }}>{ic}</div>
              <div style={{ fontFamily:"'Cormorant Garamond',serif",fontSize:22,fontWeight:700,color:"#F28C3A",marginBottom:8 }}>{n}</div>
              <div style={{ fontSize:14,color:"rgba(255,255,255,.5)",lineHeight:1.7,fontWeight:300 }}>{d}</div>
            </div>
          ))}
        </div>
      </div>

      <ChatWidget chatOpen={chatOpen} setChatOpen={setChatOpen} chatHistory={chatHistory} chatInput={chatInput} setChatInput={setChatInput} chatLoading={chatLoading} sendChat={sendChat} chatEndRef={chatEndRef}/>
    </div>
  );

  // ── LOGIN ──
  if (screen==="login") return (
    <div style={{ ...S.page,display:"flex",alignItems:"center",justifyContent:"center",minHeight:"100vh",padding:24 }}>
      <style>{G}</style>
      <div style={{ position:"relative",width:"100%",maxWidth:440 }} className="fade-up">
        <div style={{ textAlign:"center",marginBottom:36 }}>
          <div style={{ width:52,height:52,background:"linear-gradient(135deg,#E8621A,#C8922A)",borderRadius:14,display:"flex",alignItems:"center",justifyContent:"center",fontSize:24,margin:"0 auto 14px" }}>🪔</div>
          <h2 style={{ fontFamily:"'Cormorant Garamond',serif",fontSize:30,fontWeight:700,marginBottom:6,color:"var(--deep)" }}>Welcome to ChaloGhoomne</h2>
          <p style={{ color:"var(--brown)",fontSize:14 }}>Your intelligent travel companion</p>
        </div>
        <div style={{ ...S.glass,padding:"36px 32px" }}>
          {[["Full Name","text","name","Your full name"],["Email Address","email","email","you@email.com"],["Phone Number","tel","phone","+91 98765 43210"]].map(([lbl,t,key,ph])=>(
            <div key={key} style={{ marginBottom:20 }}>
              <label style={S.label}>{lbl}</label>
              <input style={S.inp} type={t} placeholder={ph} value={user[key]} onChange={e=>setUser({...user,[key]:e.target.value})}/>
              {loginErrors[key]&&<p style={{color:"#ef4444",fontSize:12,marginTop:4}}>{loginErrors[key]}</p>}
            </div>
          ))}
          <button style={{...S.btn,width:"100%",padding:15,marginTop:8}} onMouseEnter={e=>{e.target.style.transform="translateY(-2px)";e.target.style.boxShadow="0 10px 28px rgba(232,98,26,.35)"}} onMouseLeave={e=>{e.target.style.transform="";e.target.style.boxShadow=""}} onClick={()=>{ if(validateLogin()) setScreen("quiz"); }}>
            Discover My Travel Personality →
          </button>
        </div>
        <p style={{ textAlign:"center",marginTop:18,fontSize:13,color:"var(--brown)",cursor:"pointer" }} onClick={()=>setScreen("landing")}>← Back to home</p>
      </div>
      <ChatWidget chatOpen={chatOpen} setChatOpen={setChatOpen} chatHistory={chatHistory} chatInput={chatInput} setChatInput={setChatInput} chatLoading={chatLoading} sendChat={sendChat} chatEndRef={chatEndRef}/>
    </div>
  );

  // ── QUIZ ──
  if (screen==="quiz") {
    const q = PERSONALITY_QUESTIONS[quizStep];
    return (
      <div style={{ ...S.page,display:"flex",alignItems:"center",justifyContent:"center",minHeight:"100vh",padding:24 }}>
        <style>{G}</style>
        <div style={{ width:"100%",maxWidth:520 }}>
          <div style={{ marginBottom:32 }}>
            <div style={{ display:"flex",justifyContent:"space-between",marginBottom:10,fontSize:13,color:"var(--brown)" }}>
              <span>Question {quizStep+1} of {PERSONALITY_QUESTIONS.length}</span>
              <span>Hi, {user.name.split(" ")[0]} ✨</span>
            </div>
            <div style={{ height:3,background:"var(--border)",borderRadius:2 }}>
              <div style={{ height:"100%",width:`${((quizStep+1)/PERSONALITY_QUESTIONS.length)*100}%`,background:"linear-gradient(90deg,var(--saffron),var(--gold))",borderRadius:2,transition:"width .5s ease" }}/>
            </div>
          </div>
          <div className="slide-in">
            <div style={{ textAlign:"center",marginBottom:36 }}>
              <div style={{ fontSize:44,marginBottom:14 }}>{q.icon}</div>
              <h2 style={{ fontFamily:"'Cormorant Garamond',serif",fontSize:30,fontWeight:600,color:"var(--deep)",lineHeight:1.3 }}>{q.question}</h2>
            </div>
            <div style={{ display:"flex",flexDirection:"column",gap:12 }}>
              {q.options.map(opt=>(
                <div key={opt.value} style={{ ...S.glass,padding:"18px 22px",display:"flex",alignItems:"center",gap:16,cursor:"pointer",transition:"all .25s ease" }}
                  onMouseEnter={e=>{e.currentTarget.style.background="rgba(232,98,26,.08)";e.currentTarget.style.transform="translateX(6px)";e.currentTarget.style.borderColor="rgba(232,98,26,.5)"}}
                  onMouseLeave={e=>{e.currentTarget.style.background="rgba(255,255,255,.72)";e.currentTarget.style.transform="";e.currentTarget.style.borderColor="rgba(200,146,42,.18)"}}
                  onClick={()=>handleQuizAnswer(opt.value)}>
                  <span style={{fontSize:24}}>{opt.icon}</span>
                  <span style={{fontSize:16,flex:1}}>{opt.label}</span>
                  <span style={{color:"rgba(74,44,10,.3)",fontSize:18}}>→</span>
                </div>
              ))}
            </div>
          </div>
        </div>
        <ChatWidget chatOpen={chatOpen} setChatOpen={setChatOpen} chatHistory={chatHistory} chatInput={chatInput} setChatInput={setChatInput} chatLoading={chatLoading} sendChat={sendChat} chatEndRef={chatEndRef}/>
      </div>
    );
  }

  // ── SUGGESTION ──
  if (screen==="suggestion") {
    const d = DESTINATIONS[suggestedDest];
    return (
      <div style={{ ...S.page,display:"flex",alignItems:"center",justifyContent:"center",minHeight:"100vh",padding:24 }}>
        <style>{G}</style>
        <div style={{ width:"100%",maxWidth:700 }} className="fade-up">
          <div style={{ textAlign:"center",marginBottom:36 }}>
            <div style={{ fontSize:11,letterSpacing:3,textTransform:"uppercase",color:"var(--saffron)",marginBottom:12,fontWeight:500 }}>Perfect Match For You</div>
            <div style={{ fontSize:64,marginBottom:8 }}>{d.emoji}</div>
            <h1 style={{ fontFamily:"'Cormorant Garamond',serif",fontSize:52,fontWeight:700,color:"var(--deep)",marginBottom:8 }}>{d.name}</h1>
            <p style={{ color:"var(--brown)",fontSize:18 }}>{d.tagline}</p>
          </div>
          <div style={{ ...S.glass,padding:28,marginBottom:20 }}>
            <div style={{ display:"flex",gap:8,flexWrap:"wrap",marginBottom:16 }}>
              {d.tags.map(t=><span key={t} style={S.tag(d.color)}>{t}</span>)}
            </div>
            <p style={{ color:"var(--brown)",lineHeight:1.8,marginBottom:20 }}>
              Based on your travel personality, <strong style={{color:d.color}}>{d.name}</strong> is your ideal destination — perfectly matching your preferences.
            </p>
            <div style={{ display:"flex",gap:8,flexWrap:"wrap" }}>
              {d.itinerary.slice(0,3).map((item,i)=>(
                <div key={i} style={{ background:"rgba(200,146,42,.1)",border:"1px solid rgba(200,146,42,.2)",borderRadius:8,padding:"7px 14px",fontSize:13,color:"var(--brown)" }}>{item}</div>
              ))}
            </div>
          </div>
          <p style={{ fontSize:12,letterSpacing:2,textTransform:"uppercase",color:"var(--brown)",marginBottom:14,textAlign:"center",opacity:.6 }}>Or explore all 5 destinations</p>
          <div style={{ display:"grid",gridTemplateColumns:"repeat(5,1fr)",gap:10,marginBottom:24 }}>
            {Object.entries(DESTINATIONS).map(([k,d2])=>(
              <div key={k} style={{ ...S.glass,padding:"14px 10px",textAlign:"center",cursor:"pointer",transition:"all .3s",...(k===suggestedDest?{border:`1px solid ${d2.color}88`,background:`${d2.color}15`}:{}) }}
                onMouseEnter={e=>{e.currentTarget.style.transform="translateY(-4px)";}}
                onMouseLeave={e=>{e.currentTarget.style.transform="";}}
                onClick={()=>{ setChosenDest(k); setScreen("onboarding"); }}>
                <div style={{fontSize:22,marginBottom:4}}>{d2.emoji}</div>
                <div style={{fontSize:12,fontWeight:600,color:"var(--deep)"}}>{d2.name}</div>
                {k===suggestedDest&&<div style={{fontSize:10,color:d2.color,marginTop:3}}>★ Best Match</div>}
              </div>
            ))}
          </div>
          <div style={{ textAlign:"center" }}>
            <button style={S.btn} onMouseEnter={e=>{e.target.style.transform="translateY(-2px)";e.target.style.boxShadow="0 10px 28px rgba(232,98,26,.35)"}} onMouseLeave={e=>{e.target.style.transform="";e.target.style.boxShadow=""}} onClick={()=>{ setChosenDest(suggestedDest); setScreen("onboarding"); }}>
              Plan My {d.name} Trip →
            </button>
          </div>
        </div>
        <ChatWidget chatOpen={chatOpen} setChatOpen={setChatOpen} chatHistory={chatHistory} chatInput={chatInput} setChatInput={setChatInput} chatLoading={chatLoading} sendChat={sendChat} chatEndRef={chatEndRef}/>
      </div>
    );
  }

  // ── ONBOARDING (days + budget) ──
  if (screen==="onboarding") return (
    <div style={{ ...S.page,display:"flex",alignItems:"center",justifyContent:"center",minHeight:"100vh",padding:24 }}>
      <style>{G}</style>
      <div style={{ width:"100%",maxWidth:560 }} className="fade-up">
        <div style={{ textAlign:"center",marginBottom:36 }}>
          <div style={{ fontSize:40,marginBottom:10 }}>{dest.emoji}</div>
          <h2 style={{...S.h2,textAlign:"center"}}>Let's set up your {dest.name} trip</h2>
          <p style={{ color:"var(--brown)",fontSize:14 }}>Tell us the basics and we'll do the rest</p>
        </div>
        <div style={{ ...S.glass,padding:"36px 32px" }}>
          <div style={{ marginBottom:24 }}>
            <label style={S.label}>How many days are you travelling?</label>
            <div style={{ display:"flex",gap:10,flexWrap:"wrap",marginTop:4 }}>
              {[2,3,4,5,6,7].map(n=>(
                <button key={n} style={{ padding:"10px 20px",borderRadius:10,border:`1.5px solid ${days===n?"var(--saffron)":"rgba(200,146,42,.25)"}`,background:days===n?"rgba(232,98,26,.1)":"#fff",color:days===n?"var(--saffron)":"var(--brown)",fontFamily:"'DM Sans',sans-serif",fontSize:14,fontWeight:600,cursor:"pointer",transition:"all .2s" }}
                  onClick={()=>setDays(n)}>{n}D</button>
              ))}
              <input style={{ ...S.inp,width:90,textAlign:"center" }} type="number" min={1} max={30} value={days} onChange={e=>setDays(parseInt(e.target.value)||3)} placeholder="Custom"/>
            </div>
          </div>
          <div style={{ marginBottom:28 }}>
            <label style={S.label}>Your total trip budget (₹)</label>
            <input style={S.inp} type="number" placeholder="e.g. 25000" value={budget} onChange={e=>setBudget(e.target.value)}/>
            {budget && <p style={{ fontSize:12,color:"var(--brown)",marginTop:6 }}>≈ ₹{Math.round(parseFloat(budget)/days).toLocaleString()} per day for {days} days</p>}
          </div>
          <button style={{...S.btn,width:"100%",padding:15}} disabled={!budget} onMouseEnter={e=>{if(budget){e.target.style.transform="translateY(-2px)";e.target.style.boxShadow="0 10px 28px rgba(232,98,26,.35)"}}} onMouseLeave={e=>{e.target.style.transform="";e.target.style.boxShadow=""}} onClick={()=>setScreen("planMode")}>
            Continue to Plan Selection →
          </button>
        </div>
        <p style={{ textAlign:"center",marginTop:16,fontSize:13,color:"var(--brown)",cursor:"pointer" }} onClick={()=>setScreen("suggestion")}>← Back</p>
      </div>
      <ChatWidget chatOpen={chatOpen} setChatOpen={setChatOpen} chatHistory={chatHistory} chatInput={chatInput} setChatInput={setChatInput} chatLoading={chatLoading} sendChat={sendChat} chatEndRef={chatEndRef}/>
    </div>
  );

  // ── PLAN MODE ──
  if (screen==="planMode") return (
    <div style={{ ...S.page,display:"flex",alignItems:"center",justifyContent:"center",minHeight:"100vh",padding:24 }}>
      <style>{G}</style>
      <div style={{ width:"100%",maxWidth:780 }} className="fade-up">
        <div style={{ textAlign:"center",marginBottom:44 }}>
          <div style={{ fontSize:36,marginBottom:12 }}>{dest.emoji}</div>
          <h2 style={{...S.h2,textAlign:"center"}}>Plan your {dest.name} trip</h2>
          <p style={{ color:"var(--brown)" }}>{days} days · Budget ₹{parseFloat(budget).toLocaleString()}</p>
        </div>
        <div style={{ display:"grid",gridTemplateColumns:"1fr 1fr",gap:20 }}>
          {[
            { icon:"✍️",title:"Manual Planning",desc:"Choose your own transport and hotel. We'll rate your plan and show how efficient it is — helping you learn to decide better.",tags:["Your Choice","Get Rated","Learn & Improve"],tagColor:"#1A6B6B",action:()=>{ setPlanMode("manual"); setScreen("manual"); } },
            { icon:"🤖",title:"AI-Optimised Plan",desc:"Tell us your budget. Our AI selects the best transport and hotel combination and generates a detailed itinerary instantly.",tags:["AI Powered","Budget-Perfect","Fastest"],tagColor:"var(--saffron)",rec:true,action:()=>{ setPlanMode("ai"); setScreen("ai"); } },
          ].map(({icon,title,desc,tags,tagColor,rec,action})=>(
            <div key={title} style={{ ...S.glass,padding:"36px 28px",textAlign:"center",cursor:"pointer",transition:"all .35s cubic-bezier(.34,1.56,.64,1)",...(rec?{border:"1.5px solid rgba(232,98,26,.35)",background:"rgba(232,98,26,.05)"}:{}) }}
              onMouseEnter={e=>{e.currentTarget.style.transform="translateY(-6px)";e.currentTarget.style.boxShadow="0 20px 48px rgba(74,44,10,.15)"}}
              onMouseLeave={e=>{e.currentTarget.style.transform="";e.currentTarget.style.boxShadow=""}}
              onClick={action}>
              <div style={{ fontSize:44,marginBottom:16 }}>{icon}</div>
              <h3 style={{ fontFamily:"'Cormorant Garamond',serif",fontSize:24,fontWeight:700,color:"var(--deep)",marginBottom:12 }}>{title}</h3>
              <p style={{ fontSize:14,color:"var(--brown)",lineHeight:1.75,marginBottom:20 }}>{desc}</p>
              <div style={{ display:"flex",gap:8,justifyContent:"center",flexWrap:"wrap" }}>
                {tags.map(t=><span key={t} style={S.tag(tagColor)}>{t}</span>)}
              </div>
              {rec&&<div style={{ marginTop:16,background:"rgba(232,98,26,.12)",border:"1px solid rgba(232,98,26,.3)",borderRadius:8,padding:"7px 14px",fontSize:12,color:"var(--saffron)",fontWeight:600 }}>✦ Recommended</div>}
            </div>
          ))}
        </div>
        <p style={{ textAlign:"center",marginTop:24,fontSize:13,color:"var(--brown)",cursor:"pointer" }} onClick={()=>setScreen("onboarding")}>← Change days / budget</p>
      </div>
      <ChatWidget chatOpen={chatOpen} setChatOpen={setChatOpen} chatHistory={chatHistory} chatInput={chatInput} setChatInput={setChatInput} chatLoading={chatLoading} sendChat={sendChat} chatEndRef={chatEndRef}/>
    </div>
  );

  // ── MANUAL PLANNING ──
  if (screen==="manual") {
    const rating = selectedTransport&&selectedHotel&&budget ? ratePlan(selectedTransport,selectedHotel,parseFloat(budget),days) : null;
    const filtered = transportFilter==="all" ? dest.transports : dest.transports.filter(t=>t.type===transportFilter);
    return (
      <div style={{ ...S.page,minHeight:"100vh",padding:"24px 24px 80px" }}>
        <style>{G}</style>
        <div style={{ maxWidth:1000,margin:"0 auto" }}>
          {/* Header */}
          <div style={{ display:"flex",alignItems:"center",justifyContent:"space-between",padding:"20px 0 28px" }}>
            <div>
              <p style={{ fontSize:13,color:"var(--brown)",cursor:"pointer" }} onClick={()=>setScreen("planMode")}>← Back</p>
              <h2 style={{ fontFamily:"'Cormorant Garamond',serif",fontSize:28,marginTop:6,color:"var(--deep)" }}>✍️ Plan Your {dest.name} Trip</h2>
              <p style={{ fontSize:13,color:"var(--brown)",marginTop:2 }}>{days} days · Budget ₹{parseFloat(budget).toLocaleString()}</p>
            </div>
            {rating&&(
              <div style={{ textAlign:"center",animation:"fadeUp .4s ease" }}>
                <div style={{ fontSize:11,letterSpacing:2,color:"var(--brown)",marginBottom:8,fontWeight:500 }}>PLAN SCORE</div>
                <div style={{ display:"flex",gap:3,justifyContent:"center",marginBottom:5 }}>
                  {[1,2,3,4,5].map(i=><span key={i} style={{color:i<=rating.stars?"#FFD700":"rgba(74,44,10,.2)",fontSize:20}}>★</span>)}
                </div>
                <div style={{ fontSize:14,fontWeight:700,color:rating.color }}>{rating.label}</div>
              </div>
            )}
          </div>

          <div style={{ display:"grid",gridTemplateColumns:"1fr 1fr",gap:20,marginBottom:20 }}>
            {/* TRANSPORT */}
            <div style={{ ...S.glass,padding:24 }}>
              <div style={{ display:"flex",alignItems:"center",justifyContent:"space-between",marginBottom:16 }}>
                <h3 style={{ fontFamily:"'Cormorant Garamond',serif",fontSize:20,color:"var(--deep)" }}>🚀 Choose Transport</h3>
                <div style={{ display:"flex",gap:6 }}>
                  {["all","flight","train","bus"].map(f=>(
                    <button key={f} style={{ padding:"5px 12px",borderRadius:20,border:`1px solid ${transportFilter===f?"var(--saffron)":"var(--border)"}`,background:transportFilter===f?"rgba(232,98,26,.12)":"transparent",color:transportFilter===f?"var(--saffron)":"var(--brown)",fontSize:11,fontWeight:600,cursor:"pointer",textTransform:"capitalize",fontFamily:"'DM Sans',sans-serif" }} onClick={()=>setTransportFilter(f)}>{f}</button>
                  ))}
                </div>
              </div>
              <div style={{ display:"flex",flexDirection:"column",gap:10,maxHeight:340,overflowY:"auto" }}>
                {filtered.map(t=>(
                  <div key={t.id} style={{ background:selectedTransport?.id===t.id?"rgba(232,98,26,.1)":"rgba(255,255,255,.6)",border:`1.5px solid ${selectedTransport?.id===t.id?"var(--saffron)":"rgba(200,146,42,.18)"}`,borderRadius:12,padding:"13px 15px",cursor:"pointer",transition:"all .25s" }}
                    onMouseEnter={e=>{if(selectedTransport?.id!==t.id){e.currentTarget.style.borderColor="rgba(232,98,26,.4)";e.currentTarget.style.background="rgba(232,98,26,.05)";}}}
                    onMouseLeave={e=>{if(selectedTransport?.id!==t.id){e.currentTarget.style.borderColor="rgba(200,146,42,.18)";e.currentTarget.style.background="rgba(255,255,255,.6)";}}}
                    onClick={()=>setSelectedTransport(t)}>
                    <div style={{ display:"flex",justifyContent:"space-between",alignItems:"flex-start" }}>
                      <div>
                        <div style={{ display:"flex",alignItems:"center",gap:6,marginBottom:3 }}>
                          <span>{t.type==="flight"?"✈️":t.type==="train"?"🚂":"🚌"}</span>
                          <span style={{ fontWeight:600,fontSize:14,color:"var(--deep)" }}>{t.name}</span>
                          <span style={{ fontSize:10,background:`${t.type==="flight"?"#E8621A":t.type==="train"?"#1A6B6B":"#C8922A"}22`,color:t.type==="flight"?"#E8621A":t.type==="train"?"#1A6B6B":"#C8922A",padding:"2px 7px",borderRadius:20,fontWeight:600 }}>{t.class}</span>
                        </div>
                        <div style={{ fontSize:12,color:"var(--brown)" }}>{t.depart} → {t.arrive} · {t.duration}</div>
                        <div style={{ fontSize:11,color:"rgba(74,44,10,.5)",marginTop:1 }}>{t.from}</div>
                      </div>
                      <div style={{ textAlign:"right" }}>
                        <div style={{ fontSize:16,fontWeight:700,color:"#2A7A4A" }}>₹{t.price.toLocaleString()}</div>
                        {selectedTransport?.id===t.id&&<div style={{fontSize:11,color:"var(--saffron)",marginTop:3}}>✓ Selected</div>}
                      </div>
                    </div>
                  </div>
                ))}
              </div>
            </div>

            {/* HOTELS */}
            <div style={{ ...S.glass,padding:24 }}>
              <h3 style={{ fontFamily:"'Cormorant Garamond',serif",fontSize:20,color:"var(--deep)",marginBottom:16 }}>🏨 Choose Hotel</h3>
              <div style={{ display:"flex",flexDirection:"column",gap:10,maxHeight:380,overflowY:"auto" }}>
                {dest.hotels.map(h=>(
                  <div key={h.id} style={{ background:selectedHotel?.id===h.id?"rgba(232,98,26,.1)":"rgba(255,255,255,.6)",border:`1.5px solid ${selectedHotel?.id===h.id?"var(--saffron)":"rgba(200,146,42,.18)"}`,borderRadius:12,overflow:"hidden",cursor:"pointer",transition:"all .25s" }}
                    onMouseEnter={e=>{if(selectedHotel?.id!==h.id){e.currentTarget.style.borderColor="rgba(232,98,26,.4)";}}}
                    onMouseLeave={e=>{if(selectedHotel?.id!==h.id){e.currentTarget.style.borderColor="rgba(200,146,42,.18)";}}}
                    onClick={()=>setSelectedHotel(h)}>
                    <div style={{ display:"flex",gap:0 }}>
                      <img src={h.image} alt={h.name} style={{ width:80,height:80,objectFit:"cover",flexShrink:0 }}/>
                      <div style={{ padding:"10px 12px",flex:1 }}>
                        <div style={{ display:"flex",justifyContent:"space-between",alignItems:"flex-start" }}>
                          <div>
                            <div style={{ fontWeight:600,fontSize:14,color:"var(--deep)",marginBottom:2 }}>{h.name}</div>
                            <div style={{ fontSize:12,color:"var(--brown)" }}>{h.area} · ⭐ {h.rating} · {h.type}</div>
                            <div style={{ display:"flex",gap:4,flexWrap:"wrap",marginTop:4 }}>
                              {h.amenities.slice(0,2).map(a=><span key={a} style={{ fontSize:10,background:"rgba(200,146,42,.12)",padding:"2px 6px",borderRadius:4,color:"var(--brown)" }}>{a}</span>)}
                            </div>
                          </div>
                          <div style={{ textAlign:"right",flexShrink:0 }}>
                            <div style={{ fontSize:14,fontWeight:700,color:"#2A7A4A" }}>₹{h.price.toLocaleString()}</div>
                            <div style={{ fontSize:10,color:"var(--brown)" }}>/ night</div>
                            {selectedHotel?.id===h.id&&<div style={{fontSize:11,color:"var(--saffron)",marginTop:3}}>✓ Selected</div>}
                          </div>
                        </div>
                      </div>
                    </div>
                  </div>
                ))}
              </div>
            </div>
          </div>

          {/* SUMMARY & EVALUATE */}
          <div style={{ ...S.glass,padding:24,marginBottom:16 }}>
            <div style={{ display:"grid",gridTemplateColumns:"1fr 1fr 1fr auto",gap:16,alignItems:"end" }}>
              <div>
                <label style={S.label}>Transport Cost</label>
                <div style={{ fontSize:18,fontWeight:700,color:selectedTransport?"#2A7A4A":"var(--border)" }}>{selectedTransport?`₹${selectedTransport.price.toLocaleString()}`:"—"}</div>
              </div>
              <div>
                <label style={S.label}>Hotel ({days} nights)</label>
                <div style={{ fontSize:18,fontWeight:700,color:selectedHotel?"#2A7A4A":"var(--border)" }}>{selectedHotel?`₹${(selectedHotel.price*days).toLocaleString()}`:"—"}</div>
              </div>
              <div>
                <label style={S.label}>Total vs Budget</label>
                <div style={{ fontSize:18,fontWeight:700,color:totalCost(selectedTransport,selectedHotel)>parseFloat(budget||0)?"#ef4444":"#2A7A4A" }}>
                  {selectedTransport&&selectedHotel?`₹${totalCost(selectedTransport,selectedHotel).toLocaleString()}`:"—"}
                  {selectedTransport&&selectedHotel&&budget&&<span style={{fontSize:12,color:"var(--brown)",fontWeight:400}}> / ₹{parseFloat(budget).toLocaleString()}</span>}
                </div>
              </div>
              <button style={{...S.btn,padding:"13px 24px",opacity:selectedTransport&&selectedHotel&&budget?1:.5}} onClick={()=>{ if(selectedTransport&&selectedHotel&&budget) setManualResult(ratePlan(selectedTransport,selectedHotel,parseFloat(budget),days)); }}>
                Evaluate Plan
              </button>
            </div>
          </div>

          {/* RATING RESULT */}
          {manualResult&&(
            <div style={{ ...S.glass,padding:24,borderColor:manualResult.color+"44",background:manualResult.color+"0a",animation:"fadeUp .4s ease" }}>
              <div style={{ display:"flex",alignItems:"flex-start",gap:16 }}>
                <div style={{ fontSize:32 }}>{manualResult.stars>=4?"🌟":manualResult.stars>=3?"✅":"⚠️"}</div>
                <div style={{ flex:1 }}>
                  <div style={{ display:"flex",gap:3,marginBottom:8 }}>
                    {[1,2,3,4,5].map(i=><span key={i} style={{color:i<=manualResult.stars?"#FFD700":"rgba(74,44,10,.2)",fontSize:20}}>★</span>)}
                    <span style={{ fontSize:14,fontWeight:700,color:manualResult.color,marginLeft:8 }}>{manualResult.label}</span>
                  </div>
                  <p style={{ fontSize:14,color:"var(--brown)",lineHeight:1.6 }}>{manualResult.tip}</p>
                </div>
                <button style={{...S.btn,padding:"10px 20px",fontSize:13,whiteSpace:"nowrap"}} onClick={()=>{ setAiResult(null);setAiStream("");setScreen("ai"); }}>
                  See AI Plan →
                </button>
              </div>
            </div>
          )}
        </div>
        <ChatWidget chatOpen={chatOpen} setChatOpen={setChatOpen} chatHistory={chatHistory} chatInput={chatInput} setChatInput={setChatInput} chatLoading={chatLoading} sendChat={sendChat} chatEndRef={chatEndRef}/>
      </div>
    );
  }

  // ── AI PLAN ──
  if (screen==="ai") {
    const optimized = aiResult;
    return (
      <div style={{ ...S.page,minHeight:"100vh",padding:"24px 24px 80px" }}>
        <style>{G}</style>
        <div style={{ maxWidth:820,margin:"0 auto" }}>
          <div style={{ padding:"20px 0 28px" }}>
            <p style={{ fontSize:13,color:"var(--brown)",cursor:"pointer" }} onClick={()=>setScreen("planMode")}>← Back</p>
            <h2 style={{ fontFamily:"'Cormorant Garamond',serif",fontSize:28,marginTop:6,color:"var(--deep)" }}>🤖 AI-Optimised {dest.name} Plan</h2>
            <p style={{ fontSize:13,color:"var(--brown)",marginTop:2 }}>{days} days · Budget ₹{parseFloat(budget).toLocaleString()}</p>
          </div>

          {!optimized&&(
            <div style={{ ...S.glass,padding:36,marginBottom:20,animation:"fadeUp .5s ease" }}>
              <h3 style={{ fontFamily:"'Cormorant Garamond',serif",fontSize:22,color:"var(--deep)",marginBottom:8 }}>Ready to generate your AI plan</h3>
              <p style={{ fontSize:14,color:"var(--brown)",lineHeight:1.7,marginBottom:28 }}>The AI will find the most efficient transport + hotel combination within your ₹{parseFloat(budget).toLocaleString()} budget for {days} days and write a personalised itinerary.</p>
              <button style={{...S.btn,padding:"15px 36px",fontSize:15}} onMouseEnter={e=>{e.target.style.transform="translateY(-2px)";e.target.style.boxShadow="0 10px 28px rgba(232,98,26,.35)"}} onMouseLeave={e=>{e.target.style.transform="";e.target.style.boxShadow=""}} onClick={handleAIGenerate} disabled={aiLoading}>
                {aiLoading?"✦ Generating your perfect plan...":"Generate AI Plan ✦"}
              </button>
            </div>
          )}

          {aiLoading&&!optimized&&(
            <div style={{ ...S.glass,padding:36,textAlign:"center",animation:"fadeUp .4s ease" }}>
              <div style={{ fontSize:40,marginBottom:16,display:"inline-block",animation:"spin 2s linear infinite" }}>⚙️</div>
              <p style={{ color:"var(--brown)",fontSize:15 }}>Analysing flights, trains, hotels and mobility efficiency...</p>
              {aiStream&&<div style={{ marginTop:20,textAlign:"left",fontSize:14,lineHeight:1.8,color:"var(--ink)",whiteSpace:"pre-wrap",background:"rgba(232,98,26,.04)",borderRadius:12,padding:16 }}>{aiStream}</div>}
            </div>
          )}

          {optimized&&(
            <div style={{ animation:"fadeUp .5s ease" }}>
              <div style={{ display:"grid",gridTemplateColumns:"1fr 1fr",gap:16,marginBottom:16 }}>
                {[
                  { icon:optimized.transport.type==="flight"?"✈️":optimized.transport.type==="train"?"🚂":"🚌", label:"AI Selected Transport", item:optimized.transport, sub:`${optimized.transport.depart} → ${optimized.transport.arrive} · ${optimized.transport.duration}`, price:optimized.transport.price },
                  { icon:"🏨", label:"AI Selected Hotel", item:optimized.hotel, sub:`${optimized.hotel.area} · ⭐ ${optimized.hotel.rating} · ${optimized.hotel.type}`, price:optimized.hotel.price, perNight:true },
                ].map(({icon,label,sub,price,perNight})=>(
                  <div key={label} style={{ ...S.glass,padding:20 }}>
                    <div style={{ fontSize:11,letterSpacing:2,color:"#2A7A4A",fontWeight:600,marginBottom:10 }}>{label} ✓</div>
                    <div style={{ fontWeight:600,fontSize:15,color:"var(--deep)",marginBottom:5 }}>{icon} {optimized[label.includes("Transport")?"transport":"hotel"].name}</div>
                    <div style={{ fontSize:13,color:"var(--brown)",marginBottom:5 }}>{sub}</div>
                    <div style={{ fontSize:20,fontWeight:700,color:"#2A7A4A" }}>₹{price.toLocaleString()}{perNight?" / night":""}</div>
                  </div>
                ))}
              </div>

              <div style={{ ...S.glass,padding:28,marginBottom:16 }}>
                <div style={{ display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:18 }}>
                  <div style={{ fontSize:11,letterSpacing:2,color:"var(--saffron)",fontWeight:600 }}>AI GENERATED {days}-DAY ITINERARY</div>
                  <div style={{ fontSize:13,color:"var(--brown)" }}>
                    Total: <strong style={{color:"#2A7A4A"}}>₹{(optimized.transport.price+optimized.hotel.price*days).toLocaleString()}</strong>
                    <span style={{color:"rgba(74,44,10,.5)",marginLeft:6}}>of ₹{parseFloat(budget).toLocaleString()} budget</span>
                  </div>
                </div>
                <div style={{ fontSize:14,lineHeight:1.85,color:"var(--ink)",whiteSpace:"pre-wrap" }}>
                  {aiStream || "Your optimised itinerary is ready!"}
                  {aiLoading&&<span style={{borderRight:"2px solid var(--saffron)",marginLeft:2,animation:"pulse .8s ease infinite"}}> </span>}
                </div>
              </div>

              <div style={{ display:"flex",gap:12,justifyContent:"center" }}>
                <button style={S.btn} onMouseEnter={e=>{e.target.style.transform="translateY(-2px)";e.target.style.boxShadow="0 10px 28px rgba(232,98,26,.35)"}} onMouseLeave={e=>{e.target.style.transform="";e.target.style.boxShadow=""}} onClick={()=>{ setAiResult(null);setAiStream(""); }}>
                  ↺ Re-optimise
                </button>
                <button style={S.btnOut} onMouseEnter={e=>e.target.style.background="var(--cream)"} onMouseLeave={e=>e.target.style.background="transparent"} onClick={()=>setScreen("manual")}>
                  Try Manual Planning
                </button>
              </div>
            </div>
          )}
        </div>
        <ChatWidget chatOpen={chatOpen} setChatOpen={setChatOpen} chatHistory={chatHistory} chatInput={chatInput} setChatInput={setChatInput} chatLoading={chatLoading} sendChat={sendChat} chatEndRef={chatEndRef}/>
      </div>
    );
  }

  return null;
}

// ── CHAT WIDGET ──────────────────────────────────────────────────────────────
function ChatWidget({ chatOpen, setChatOpen, chatHistory, chatInput, setChatInput, chatLoading, sendChat, chatEndRef }) {
  return (
    <>
      {/* Chat Panel */}
      {chatOpen&&(
        <div style={{ position:"fixed",bottom:96,right:24,width:360,background:"#fff",border:"1.5px solid rgba(200,146,42,.25)",borderRadius:20,boxShadow:"0 20px 60px rgba(74,44,10,.18)",zIndex:1000,display:"flex",flexDirection:"column",overflow:"hidden",animation:"chatPop .3s ease" }}>
          <div style={{ background:"linear-gradient(135deg,#E8621A,#C45C30)",padding:"16px 20px",display:"flex",justifyContent:"space-between",alignItems:"center" }}>
            <div>
              <div style={{ fontFamily:"'Cormorant Garamond',serif",fontSize:16,fontWeight:700,color:"#fff" }}>🪔 Travel Assistant</div>
              <div style={{ fontSize:11,color:"rgba(255,255,255,.75)",marginTop:1 }}>Powered by ChaloGhoomne AI</div>
            </div>
            <button style={{ background:"rgba(255,255,255,.2)",border:"none",color:"#fff",width:28,height:28,borderRadius:"50%",cursor:"pointer",fontSize:14,display:"flex",alignItems:"center",justifyContent:"center" }} onClick={()=>setChatOpen(false)}>✕</button>
          </div>
          <div style={{ flex:1,maxHeight:320,overflowY:"auto",padding:16,display:"flex",flexDirection:"column",gap:10 }}>
            {chatHistory.map((m,i)=>(
              <div key={i} style={{ display:"flex",justifyContent:m.role==="user"?"flex-end":"flex-start" }}>
                <div style={{ maxWidth:"82%",padding:"10px 14px",borderRadius:m.role==="user"?"16px 16px 4px 16px":"16px 16px 16px 4px",background:m.role==="user"?"linear-gradient(135deg,#E8621A,#C45C30)":"rgba(250,246,239,1)",color:m.role==="user"?"#fff":"var(--ink)",fontSize:13,lineHeight:1.6,border:m.role==="user"?"none":"1px solid rgba(200,146,42,.18)" }}>
                  {m.text}
                </div>
              </div>
            ))}
            {chatLoading&&(
              <div style={{ display:"flex",justifyContent:"flex-start" }}>
                <div style={{ padding:"10px 14px",borderRadius:"16px 16px 16px 4px",background:"rgba(250,246,239,1)",border:"1px solid rgba(200,146,42,.18)",fontSize:13,color:"var(--brown)" }}>
                  <span style={{animation:"pulse 1s ease infinite",display:"inline-block"}}>✦ Thinking...</span>
                </div>
              </div>
            )}
            <div ref={chatEndRef}/>
          </div>
          <div style={{ padding:"12px 16px",borderTop:"1px solid rgba(200,146,42,.15)",display:"flex",gap:8 }}>
            <input style={{ ...{ width:"100%",background:"rgba(250,246,239,.8)",border:"1.5px solid rgba(200,146,42,.25)",color:"var(--ink)",padding:"10px 14px",borderRadius:10,fontSize:14,fontFamily:"'DM Sans',sans-serif" } }} placeholder="Ask about destinations, budgets..." value={chatInput} onChange={e=>setChatInput(e.target.value)} onKeyDown={e=>e.key==="Enter"&&sendChat()}/>
            <button style={{ background:"linear-gradient(135deg,#E8621A,#C45C30)",border:"none",color:"#fff",width:40,height:40,borderRadius:10,cursor:"pointer",fontSize:16,flexShrink:0 }} onClick={sendChat}>→</button>
          </div>
        </div>
      )}
      {/* FAB */}
      <button style={{ position:"fixed",bottom:24,right:24,width:56,height:56,background:"linear-gradient(135deg,#E8621A,#C45C30)",border:"none",borderRadius:"50%",cursor:"pointer",zIndex:1001,boxShadow:"0 8px 28px rgba(232,98,26,.4)",display:"flex",alignItems:"center",justifyContent:"center",fontSize:24,transition:"all .3s" }} onMouseEnter={e=>{e.currentTarget.style.transform="scale(1.1)";e.currentTarget.style.boxShadow="0 12px 36px rgba(232,98,26,.5)"}} onMouseLeave={e=>{e.currentTarget.style.transform="";e.currentTarget.style.boxShadow="0 8px 28px rgba(232,98,26,.4)"}} onClick={()=>setChatOpen(o=>!o)} title="Chat with AI Travel Assistant">
        {chatOpen?"✕":"🪔"}
      </button>
    </>
  );
}
