<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ZAMoney - Perfect Trading Terminal</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- React & ReactDOM -->
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <!-- Babel for JSX -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700;800;900&display=swap');
        
        :root {
            --brand-blue: #3b82f6;
            --brand-dark: #050505;
        }

        body { 
            font-family: 'Inter', sans-serif; 
            background-color: var(--brand-dark); 
            color: white; 
            margin: 0; 
            overflow-x: hidden;
            -webkit-tap-highlight-color: transparent;
        }

        .premium-glass { 
            background: rgba(255, 255, 255, 0.03); 
            backdrop-filter: blur(20px); 
            border: 1px solid rgba(255, 255, 255, 0.08); 
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.8);
        }

        .neon-glow {
            box-shadow: 0 0 20px rgba(59, 130, 246, 0.4);
        }

        .no-scrollbar::-webkit-scrollbar { display: none; }
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }

        @keyframes pulse-slow {
            0%, 100% { opacity: 1; transform: scale(1); }
            50% { opacity: 0.8; transform: scale(1.05); }
        }

        .animate-pulse-slow { animation: pulse-slow 3s infinite ease-in-out; }
        
        .tab-active { color: #3b82f6; position: relative; }
        .tab-active::after {
            content: '';
            position: absolute;
            bottom: -8px;
            left: 50%;
            transform: translateX(-50%);
            width: 4px;
            height: 4px;
            border-radius: 50%;
            background: #3b82f6;
            box-shadow: 0 0 10px #3b82f6;
        }
    </style>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect, useRef, useMemo } = React;

        // Komponen Ikon Universal
        const Icon = ({ name, size = 24, className = "", strokeWidth = 2.5 }) => {
            useEffect(() => { if (window.lucide) window.lucide.createIcons(); }, [name]);
            return <i data-lucide={name} style={{ width: size, height: size }} className={className} strokeWidth={strokeWidth}></i>;
        };

        // Komponen Grafik High-End menggunakan Chart.js
        const AdvancedChart = ({ data }) => {
            const chartRef = useRef(null);
            const chartInstance = useRef(null);

            useEffect(() => {
                if (chartRef.current) {
                    const ctx = chartRef.current.getContext('2d');
                    if (chartInstance.current) chartInstance.current.destroy();

                    const grad = ctx.createLinearGradient(0, 0, 0, 400);
                    grad.addColorStop(0, 'rgba(59, 130, 246, 0.4)');
                    grad.addColorStop(1, 'rgba(59, 130, 246, 0)');

                    chartInstance.current = new Chart(ctx, {
                        type: 'line',
                        data: {
                            labels: data.map(d => d.label),
                            datasets: [{
                                data: data.map(d => d.val),
                                borderColor: '#3b82f6',
                                borderWidth: 3,
                                fill: true,
                                backgroundColor: grad,
                                tension: 0.45,
                                pointRadius: 0,
                                pointHoverRadius: 6,
                                pointHoverBackgroundColor: '#3b82f6',
                                pointHoverBorderColor: '#fff',
                                pointHoverBorderWidth: 2
                            }]
                        },
                        options: {
                            responsive: true,
                            maintainAspectRatio: false,
                            plugins: { legend: { display: false }, tooltip: { mode: 'index', intersect: false } },
                            scales: { x: { display: false }, y: { display: false } }
                        }
                    });
                }
            }, [data]);
            return <canvas ref={chartRef}></canvas>;
        };

        const App = () => {
            const [view, setView] = useState('landing');
            const [isLoading, setIsLoading] = useState(false);
            const [userBalance, setUserBalance] = useState(24580.65);
            const [activeTradeType, setActiveTradeType] = useState('buy');

            const marketHistory = useMemo(() => [
                { label: '1H', val: 61000 }, { label: '2H', val: 62500 },
                { label: '3H', val: 61200 }, { label: '4H', val: 63800 },
                { label: '5H', val: 64100 }, { label: '6H', val: 63500 },
                { label: '7H', val: 64231 }
            ], []);

            const coins = [
                { id: 'btc', name: 'Bitcoin', pair: 'BTC/USDT', price: '64,231.50', change: '+2.45%', up: true },
                { id: 'eth', name: 'Ethereum', pair: 'ETH/USDT', price: '3,452.12', change: '+1.12%', up: true },
                { id: 'sol', name: 'Solana', pair: 'SOL/USDT', price: '145.82', change: '-0.85%', up: false },
                { id: 'bnb', name: 'BNB', pair: 'BNB/USDT', price: '592.40', change: '+0.50%', up: true },
            ];

            const navigate = (to) => {
                setIsLoading(true);
                setTimeout(() => {
                    setView(to);
                    setIsLoading(false);
                }, 400);
            };

            // Halaman Landing
            const Landing = () => (
                <div className="min-h-screen bg-black flex flex-col items-center justify-center p-10 relative overflow-hidden">
                    <div className="absolute top-[-10%] left-[-10%] w-[50%] h-[40%] bg-blue-600/10 blur-[120px] rounded-full"></div>
                    <div className="absolute bottom-[-10%] right-[-10%] w-[50%] h-[40%] bg-blue-900/10 blur-[120px] rounded-full"></div>
                    
                    <div className="z-10 flex flex-col items-center">
                        <div className="w-24 h-24 bg-blue-600 rounded-[2.5rem] flex items-center justify-center mb-10 shadow-[0_0_60px_rgba(37,99,235,0.4)] animate-pulse-slow">
                            <Icon name="trending-up" size={48} color="white" />
                        </div>
                        <h1 className="text-6xl font-black italic tracking-tighter uppercase mb-2">ZAMoney</h1>
                        <p className="text-slate-500 font-bold uppercase tracking-[0.5em] text-[10px] mb-20 italic">The Next Gen Terminal</p>
                        
                        <div className="w-full max-w-xs space-y-4">
                            <button onClick={() => navigate('login')} className="w-full bg-blue-600 py-5 rounded-2xl font-black uppercase text-[11px] tracking-widest shadow-xl active:scale-95 transition-transform">Masuk Terminal</button>
                            <button className="w-full premium-glass py-5 rounded-2xl font-black uppercase text-[11px] tracking-widest text-slate-300">Buat Akun Pro</button>
                        </div>
                    </div>
                </div>
            );

            // Halaman Login
            const Login = () => (
                <div className="min-h-screen bg-black p-8 pt-24 flex flex-col">
                    <button onClick={() => navigate('landing')} className="mb-12 text-slate-500 flex items-center gap-2 font-black uppercase text-[10px] tracking-widest">
                        <Icon name="chevron-left" size={16} /> Kembali
                    </button>
                    <h2 className="text-5xl font-black italic uppercase tracking-tighter mb-2">Otentikasi</h2>
                    <p className="text-slate-400 text-[10px] font-black uppercase tracking-[0.4em] mb-16">Hubungkan Dompet Digital Anda</p>
                    
                    <div className="space-y-8 flex-1">
                        <div className="space-y-3">
                            <label className="text-[10px] font-black uppercase tracking-widest text-slate-500 ml-2">ID Terminal</label>
                            <input type="text" placeholder="ZAM-992031" className="w-full premium-glass rounded-2xl p-6 text-sm font-bold outline-none focus:border-blue-500 transition-all placeholder:opacity-30 text-white" />
                        </div>
                        <div className="space-y-3">
                            <label className="text-[10px] font-black uppercase tracking-widest text-slate-500 ml-2">Kode Enkripsi</label>
                            <input type="password" placeholder="••••••••" className="w-full premium-glass rounded-2xl p-6 text-sm font-bold outline-none focus:border-blue-500 transition-all placeholder:opacity-30 text-white" />
                        </div>
                        <button onClick={() => navigate('dashboard')} className="w-full bg-blue-600 py-6 rounded-3xl font-black uppercase text-[11px] italic tracking-widest shadow-2xl shadow-blue-600/30 active:scale-95 transition-transform">Masuk Sekarang</button>
                    </div>
                </div>
            );

            // Halaman Dashboard Utama
            const Dashboard = () => (
                <div className="p-6 pb-32 space-y-10">
                    <div className="flex justify-between items-center">
                        <div className="flex items-center gap-4">
                            <div className="w-12 h-12 rounded-full overflow-hidden border-2 border-blue-500/50 p-0.5 shadow-[0_0_15px_rgba(59,130,246,0.3)]">
                                <div className="w-full h-full bg-slate-800 rounded-full flex items-center justify-center font-black text-blue-400">ZA</div>
                            </div>
                            <div>
                                <p className="text-[9px] text-slate-500 font-black uppercase tracking-widest italic">Selamat Datang,</p>
                                <h4 className="font-black italic text-sm">ZAMoney Elite</h4>
                            </div>
                        </div>
                        <div className="flex gap-3">
                            <div className="w-12 h-12 premium-glass rounded-2xl flex items-center justify-center text-slate-400 hover:text-blue-400 transition-colors"><Icon name="search" size={20}/></div>
                            <div className="w-12 h-12 premium-glass rounded-2xl flex items-center justify-center text-slate-400 hover:text-blue-400 transition-colors relative">
                                <Icon name="bell" size={20}/>
                                <span className="absolute top-3 right-3 w-2 h-2 bg-rose-500 rounded-full border-2 border-black"></span>
                            </div>
                        </div>
                    </div>

                    <div className="space-y-2">
                        <p className="text-[11px] text-slate-500 font-black uppercase tracking-[0.3em] text-center">Total Estimasi Aset</p>
                        <h2 className="text-5xl font-black italic tracking-tighter text-center">${userBalance.toLocaleString()}</h2>
                        <div className="flex justify-center items-center gap-2 text-emerald-500 font-black italic text-[11px] uppercase tracking-widest">
                            <Icon name="trending-up" size={12} /> +12.50% ($2,120)
                        </div>
                    </div>

                    <div className="flex justify-between items-center gap-4">
                        {[
                            { n: 'Deposit', i: 'plus-square' },
                            { n: 'Tarik', i: 'arrow-down-right' },
                            { n: 'Swap', i: 'repeat' },
                            { n: 'Kirim', i: 'send' }
                        ].map(act => (
                            <div key={act.n} className="flex flex-col items-center gap-3 flex-1">
                                <button className="w-full aspect-square premium-glass rounded-3xl flex items-center justify-center text-blue-500 hover:bg-blue-600 hover:text-white transition-all shadow-lg active:scale-90">
                                    <Icon name={act.i} size={22} />
                                </button>
                                <span className="text-[9px] font-black uppercase tracking-widest text-slate-400 italic">{act.n}</span>
                            </div>
                        ))}
                    </div>

                    <div className="space-y-6">
                        <div className="flex justify-between items-end">
                            <h3 className="text-xs font-black uppercase tracking-[0.3em] text-slate-500">Live Markets</h3>
                            <button className="text-[10px] text-blue-500 font-black uppercase italic tracking-widest">Lihat Semua</button>
                        </div>
                        {coins.map((coin, i) => (
                            <div key={i} onClick={() => navigate('trading')} className="premium-glass p-5 rounded-[2.5rem] flex justify-between items-center hover:bg-white/5 active:scale-95 transition-all">
                                <div className="flex items-center gap-4">
                                    <div className="w-12 h-12 bg-black rounded-2xl flex items-center justify-center border border-white/10 shadow-inner">
                                        <span className="text-blue-500 font-black italic text-lg">{coin.name.charAt(0)}</span>
                                    </div>
                                    <div>
                                        <p className="text-sm font-black italic tracking-tight">{coin.pair}</p>
                                        <p className="text-[10px] font-bold text-slate-500 uppercase">{coin.name}</p>
                                    </div>
                                </div>
                                <div className="text-right">
                                    <p className="text-sm font-mono font-bold italic tracking-tight">${coin.price}</p>
                                    <p className={`text-[10px] font-black ${coin.up ? 'text-emerald-500' : 'text-rose-500'}`}>{coin.change}</p>
                                </div>
                            </div>
                        ))}
                    </div>
                </div>
            );

            // Halaman Trading Terminal
            const Trading = () => (
                <div className="flex flex-col h-full">
                    <div className="p-6 border-b border-white/5 flex justify-between items-center premium-glass">
                        <div className="flex items-center gap-4">
                            <button onClick={() => navigate('dashboard')} className="w-10 h-10 premium-glass rounded-xl flex items-center justify-center text-white"><Icon name="chevron-left" size={18}/></button>
                            <div>
                                <h2 className="text-xl font-black italic uppercase tracking-tighter leading-none">BTC / USDT</h2>
                                <p className="text-[9px] text-emerald-500 font-black uppercase tracking-widest mt-1">Live Terminal</p>
                            </div>
                        </div>
                        <div className="text-right">
                            <p className="text-lg font-mono font-bold text-emerald-500 tracking-tighter leading-none">$64,231.50</p>
                            <p className="text-[9px] text-slate-500 font-black uppercase tracking-widest">+2.45%</p>
                        </div>
                    </div>
                    
                    <div className="flex-1 p-6 overflow-y-auto no-scrollbar pb-32">
                        <div className="h-72 premium-glass rounded-[3rem] p-6 mb-8 relative overflow-hidden">
                            <div className="absolute top-4 left-6 flex gap-4 z-10">
                                {['1H', '1D', '1W', '1M', '1Y'].map(t => (
                                    <button key={t} className={`text-[10px] font-black px-2 py-1 rounded-lg ${t === '1H' ? 'bg-blue-600 text-white' : 'text-slate-500'}`}>{t}</button>
                                ))}
                            </div>
                            <AdvancedChart data={marketHistory} />
                        </div>

                        <div className="premium-glass p-8 rounded-[3.5rem] space-y-8">
                            <div className="flex bg-black/50 p-2 rounded-2xl border border-white/5">
                                <button onClick={() => setActiveTradeType('buy')} className={`flex-1 py-4 rounded-xl text-xs font-black uppercase italic transition-all ${activeTradeType === 'buy' ? 'bg-emerald-500 text-white shadow-xl shadow-emerald-500/20' : 'text-slate-500'}`}>Beli</button>
                                <button onClick={() => setActiveTradeType('sell')} className={`flex-1 py-4 rounded-xl text-xs font-black uppercase italic transition-all ${activeTradeType === 'sell' ? 'bg-rose-500 text-white shadow-xl shadow-rose-500/20' : 'text-slate-500'}`}>Jual</button>
                            </div>

                            <div className="space-y-4">
                                <div className="flex justify-between px-2">
                                    <span className="text-[10px] font-black uppercase tracking-widest text-slate-500">Jumlah Trade</span>
                                    <span className="text-[10px] font-black uppercase tracking-widest text-blue-500">Saldo: $24,580</span>
                                </div>
                                <div className="relative">
                                    <input type="number" placeholder="0.00" className="w-full bg-black border border-white/10 rounded-2xl p-7 text-2xl font-black italic outline-none focus:border-blue-500 text-white placeholder:opacity-20" />
                                    <div className="absolute right-6 top-1/2 -translate-y-1/2 flex flex-col items-end">
                                        <span className="font-black text-slate-500 uppercase text-xs">BTC</span>
                                        <span className="text-[8px] text-slate-600 font-bold uppercase mt-1">≈ $0.00</span>
                                    </div>
                                </div>
                                <div className="grid grid-cols-4 gap-3">
                                    {[25, 50, 75, 100].map(p => (
                                        <button key={p} className="premium-glass py-3 rounded-xl text-[10px] font-black text-slate-400 hover:text-blue-500 transition-colors">{p}%</button>
                                    ))}
                                </div>
                            </div>

                            <button className={`w-full py-6 rounded-[2rem] text-[11px] font-black uppercase italic tracking-[0.3em] shadow-2xl active:scale-95 transition-all ${activeTradeType === 'buy' ? 'bg-emerald-500 shadow-emerald-500/20 text-white' : 'bg-rose-500 shadow-rose-500/20 text-white'}`}>
                                Konfirmasi {activeTradeType === 'buy' ? 'Pembelian' : 'Penjualan'}
                            </button>
                        </div>
                    </div>
                </div>
            );

            // Halaman Aset/Dompet
            const Assets = () => (
                <div className="p-6 pb-32 space-y-8">
                    <h2 className="text-4xl font-black italic uppercase tracking-tighter">Portofolio</h2>
                    <div className="premium-glass p-8 rounded-[3rem] space-y-6 relative overflow-hidden">
                        <div className="absolute top-0 right-0 w-32 h-32 bg-blue-600/10 blur-3xl"></div>
                        <div className="relative">
                            <p className="text-[10px] font-black uppercase tracking-widest text-slate-500 mb-2">Total Nilai Bersih</p>
                            <h3 className="text-4xl font-black italic tracking-tighter text-white">$24,580.65</h3>
                            <div className="h-2 w-full bg-white/5 rounded-full mt-8 overflow-hidden flex">
                                <div className="h-full bg-orange-400 w-[60%]"></div>
                                <div className="h-full bg-blue-500 w-[25%]"></div>
                                <div className="h-full bg-emerald-400 w-[15%]"></div>
                            </div>
                        </div>
                    </div>
                    
                    <div className="space-y-4">
                        <h3 className="text-[11px] font-black uppercase tracking-widest text-slate-500">Alokasi Aset</h3>
                        {[
                            { n: 'Bitcoin', s: 'BTC', v: '0.45 BTC', d: '$14,748', p: '60%', c: 'bg-orange-400' },
                            { n: 'Ethereum', s: 'ETH', v: '2.5 ETH', d: '$8,630', p: '35%', c: 'bg-blue-500' },
                            { n: 'USDT', s: 'USDT', v: '1,202.65', d: '$1,202', p: '5%', c: 'bg-emerald-500' }
                        ].map(asset => (
                            <div key={asset.s} className="premium-glass p-6 rounded-[2rem] flex justify-between items-center">
                                <div className="flex items-center gap-4">
                                    <div className={`w-2 h-10 ${asset.c} rounded-full`}></div>
                                    <div>
                                        <p className="text-sm font-black italic tracking-tight">{asset.n}</p>
                                        <p className="text-[10px] font-bold text-slate-500">{asset.v}</p>
                                    </div>
                                </div>
                                <div className="text-right">
                                    <p className="text-sm font-mono font-bold tracking-tight text-white">{asset.d}</p>
                                    <p className="text-[9px] font-black text-blue-500">{asset.p}</p>
                                </div>
                            </div>
                        ))}
                    </div>
                </div>
            );

            // Halaman Keuangan/Finance
            const Finance = () => (
                <div className="p-6 pb-32 space-y-8">
                    <h2 className="text-4xl font-black italic uppercase tracking-tighter">Finance</h2>
                    <div className="grid grid-cols-2 gap-4">
                        <div className="premium-glass p-6 rounded-[2.5rem] space-y-4">
                            <div className="w-10 h-10 bg-emerald-500/20 rounded-xl flex items-center justify-center text-emerald-500"><Icon name="pie-chart" size={20}/></div>
                            <div>
                                <h4 className="text-[11px] font-black uppercase italic tracking-widest">Staking</h4>
                                <p className="text-[9px] text-slate-500 font-bold mt-1">Estimasi APY 12%</p>
                            </div>
                        </div>
                        <div className="premium-glass p-6 rounded-[2.5rem] space-y-4">
                            <div className="w-10 h-10 bg-orange-500/20 rounded-xl flex items-center justify-center text-orange-500"><Icon name="landmark" size={20}/></div>
                            <div>
                                <h4 className="text-[11px] font-black uppercase italic tracking-widest">Pinjaman</h4>
                                <p className="text-[9px] text-slate-500 font-bold mt-1">Bunga rendah 2.5%</p>
                            </div>
                        </div>
                    </div>
                    <div className="premium-glass p-8 rounded-[3rem] border-blue-500/20">
                        <h3 className="text-sm font-black italic uppercase mb-4">ZAMoney Card</h3>
                        <div className="aspect-[1.6/1] w-full bg-gradient-to-br from-blue-600 via-blue-700 to-slate-900 rounded-3xl p-6 flex flex-col justify-between shadow-2xl relative overflow-hidden">
                            <div className="absolute top-0 right-0 w-32 h-32 bg-white/5 rotate-45 translate-x-10 -translate-y-10"></div>
                            <div className="flex justify-between items-start">
                                <Icon name="contactless" size={24} color="white" />
                                <span className="text-[10px] font-black tracking-widest">PLATINUM</span>
                            </div>
                            <div>
                                <p className="text-lg font-mono tracking-[0.2em] mb-4">•••• •••• •••• 9920</p>
                                <div className="flex justify-between items-end">
                                    <div>
                                        <p className="text-[8px] opacity-60 uppercase mb-1">Card Holder</p>
                                        <p className="text-xs font-black tracking-widest">ZA ELITE USER</p>
                                    </div>
                                    <div className="w-10 h-6 bg-orange-400/20 rounded flex items-center justify-center overflow-hidden">
                                        <div className="w-4 h-full bg-orange-400 opacity-40"></div>
                                        <div className="w-4 h-full bg-orange-400"></div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            );

            // Halaman Admin Panel
            const Admin = () => (
                <div className="p-6 pb-32 space-y-8">
                    <div className="flex justify-between items-center">
                        <h2 className="text-4xl font-black italic uppercase tracking-tighter">Admin Panel</h2>
                        <div className="px-3 py-1 bg-blue-600/20 rounded-full border border-blue-500/30 text-[9px] font-black text-blue-500 tracking-widest uppercase">Root</div>
                    </div>
                    <div className="space-y-4">
                        <div className="premium-glass p-6 rounded-[2.5rem] flex items-center gap-6">
                            <div className="w-14 h-14 bg-blue-600/10 rounded-2xl flex items-center justify-center text-blue-500"><Icon name="shield" size={28}/></div>
                            <div>
                                <h4 className="font-black italic text-sm">Keamanan Lapis-3</h4>
                                <p className="text-[9px] text-emerald-500 font-black uppercase tracking-widest mt-1">Aktif & Terenkripsi</p>
                            </div>
                        </div>
                    </div>
                    <div className="premium-glass p-8 rounded-[3rem] space-y-6">
                        <h3 className="text-xs font-black uppercase tracking-[0.3em] text-slate-500">Log Aktivitas Terbaru</h3>
                        {[1, 2, 3].map(i => (
                            <div key={i} className="flex justify-between items-center py-3 border-b border-white/5 last:border-0">
                                <div className="flex gap-4 items-center">
                                    <div className="w-2 h-2 bg-blue-500 rounded-full"></div>
                                    <div>
                                        <p className="text-[11px] font-black italic tracking-tight">Login Terdeteksi di Jakarta, ID</p>
                                        <p className="text-[9px] text-slate-600 font-bold uppercase mt-1">10 Menit yang lalu</p>
                                    </div>
                                </div>
                                <Icon name="chevron-right" size={14} className="text-slate-700" />
                            </div>
                        ))}
                    </div>
                </div>
            );

            return (
                <div className="h-screen bg-black flex flex-col overflow-hidden max-w-md mx-auto relative border-x border-white/5">
                    {/* Overlay Loading */}
                    {isLoading && (
                        <div className="absolute inset-0 bg-black/80 backdrop-blur-md z-[200] flex items-center justify-center">
                            <div className="w-12 h-12 border-4 border-blue-500/20 border-t-blue-500 rounded-full animate-spin"></div>
                        </div>
                    )}

                    <main className="flex-1 overflow-y-auto no-scrollbar">
                        {view === 'landing' && <Landing />}
                        {view === 'login' && <Login />}
                        {view === 'dashboard' && <Dashboard />}
                        {view === 'trading' && <Trading />}
                        {view === 'saldo' && <Assets />}
                        {view === 'finance' && <Finance />}
                        {view === 'admin' && <Admin />}
                    </main>

                    {/* Navigasi Bar Premium */}
                    {['dashboard', 'trading', 'saldo', 'finance', 'admin'].includes(view) && (
                        <nav className="absolute bottom-0 left-0 right-0 h-24 premium-glass border-t border-white/10 px-4 flex justify-around items-center z-[100] rounded-t-[3rem]">
                            {[
                                { id: 'dashboard', icon: 'layout-dashboard', label: 'Beranda' },
                                { id: 'trading', icon: 'trending-up', label: 'Trade' },
                                { id: 'saldo', icon: 'wallet', label: 'Aset' },
                                { id: 'finance', icon: 'refresh-ccw', label: 'Finance' },
                                { id: 'admin', icon: 'shield-check', label: 'Admin' }
                            ].map((item) => (
                                <button key={item.id} onClick={() => navigate(item.id)}
                                    className={`flex flex-col items-center flex-1 transition-all ${view === item.id ? 'tab-active scale-110' : 'text-slate-600 hover:text-slate-400'}`}>
                                    <Icon name={item.icon} size={20} strokeWidth={view === item.id ? 2.5 : 2} />
                                    <span className="text-[7px] font-black uppercase mt-2 tracking-widest">{item.label}</span>
                                </button>
                            ))}
                        </nav>
                    )}
                </div>
            );
        };

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>
