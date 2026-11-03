---
---
---
---
<!DOCTYPE html><!DOCTYPE html>
<html lang="en" class="scroll-smooth">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Digital Wedding Invitation - Fazal Azeem & Family</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts for High-End Typography & Arabic Calligraphy -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Amiri:ital,wght@0,400;0,700;1,400&family=Cormorant+Garamond:ital,wght@0,300;0,400;0,500;0,600;0,700;1,400&family=Montserrat:wght@200;300;400;500;600&family=Alex+Brush&display=swap" rel="stylesheet">
    <!-- FontAwesome for Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <!-- Firebase Imports for Realtime Cloud Database -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, addDoc, onSnapshot } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Firebase Configuration & Initialization
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'wedding-invitation-app';
        const firebaseConfig = typeof __firebase_config !== 'undefined' 
            ? JSON.parse(__firebase_config) 
            : { apiKey: "demo-key", authDomain: "demo.firebaseapp.com", projectId: "demo-project" };

        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);

        let currentUser = null;

        // Authenticate user before performing Firestore queries
        async function initAuth() {
            try {
                if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                    const userCredential = await signInWithCustomToken(auth, __initial_auth_token);
                    currentUser = userCredential.user;
                } else {
                    const userCredential = await signInAnonymously(auth);
                    currentUser = userCredential.user;
                }
                listenToWishes();
            } catch (error) {
                console.error("Auth / Firestore initialization error:", error);
            }
        }

        // Realtime Listener for Private Wishes
        window.allWishesList = [];
        function listenToWishes() {
            if (!currentUser) return;
            const wishesCollection = collection(db, 'artifacts', appId, 'public', 'data', 'private_wishes');
            
            onSnapshot(wishesCollection, (snapshot) => {
                const wishes = [];
                snapshot.forEach(doc => {
                    wishes.push({ id: doc.id, ...doc.data() });
                });

                wishes.sort((a, b) => (b.timestamp || 0) - (a.timestamp || 0));
                window.allWishesList = wishes;
                
                if (window.isAdminLoggedIn) {
                    renderAdminWishes();
                }
            }, (error) => {
                console.error("Error fetching wishes:", error);
            });
        }

        // Save Wish Privately to Cloud Firestore
        window.saveWishToCloud = async function(name, wish) {
            if (!currentUser) {
                await initAuth();
            }
            if (!currentUser) return;

            const wishesCollection = collection(db, 'artifacts', appId, 'public', 'data', 'private_wishes');
            const now = new Date();
            const dateStr = now.toLocaleDateString('en-US', { month: 'short', day: '2-digit', year: 'numeric' });

            await addDoc(wishesCollection, {
                name: name,
                wish: wish,
                date: dateStr,
                timestamp: Date.now()
            });
        };

        window.addEventListener('DOMContentLoaded', initAuth);
    </script>
    
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        cream: '#FDFBF7',
                        champagne: '#F5EFEB',
                        gold: '#C5A059',
                        darkGreen: '#1a271f',
                        goldLight: '#E6C786',
                        envelopeBg: '#bc9b7b',
                        envelopeDark: '#a38363'
                    },
                    fontFamily: {
                        serif: ['"Cormorant Garamond"', 'serif'],
                        sans: ['"Montserrat"', 'sans-serif'],
                        script: ['"Alex Brush"', 'cursive'],
                        arabic: ['"Amiri"', 'serif']
                    }
                }
            }
        }
    </script>
    <style>
        @keyframes floatSlow {
            0%, 100% { transform: translateY(0px) rotate(0deg); }
            50% { transform: translateY(-12px) rotate(0.5deg); }
        }
        .animate-float {
            animation: floatSlow 4s ease-in-out infinite;
        }

        @keyframes pulseGlow {
            0%, 100% { box-shadow: 0 0 15px rgba(139, 0, 0, 0.4); }
            50% { box-shadow: 0 0 25px rgba(186, 12, 12, 0.8); }
        }
        .wax-seal {
            animation: pulseGlow 2.5s infinite;
        }

        .envelope-container {
            perspective: 1000px;
        }

        #scratchCanvas {
            touch-action: none;
            cursor: pointer;
        }

        .page-section {
            transition: opacity 0.5s ease-in-out, transform 0.5s ease-in-out;
        }

        .custom-scrollbar::-webkit-scrollbar {
            width: 4px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
            background: rgba(0, 0, 0, 0.05);
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
            background: #C5A059;
            border-radius: 4px;
        }
    </style>
</head>
<body class="bg-cream font-sans text-gray-800 antialiased selection:bg-gold selection:text-white min-h-screen flex flex-col justify-between overflow-x-hidden">

    <!-- ========================================== -->
    <!-- 1. ENVELOPE LANDING OVERLAY                -->
    <!-- ========================================== -->
    <div id="envelopeOverlay" class="fixed inset-0 z-50 flex flex-col justify-between items-center bg-gradient-to-b from-[#f2e6d8] via-[#dfccb7] to-[#ba9c80] p-6 transition-all duration-1000">
        <div class="text-center pt-8 tracking-widest text-gray-700">
            <h1 class="font-serif text-2xl md:text-3xl tracking-[0.25em] uppercase text-gray-800">Fazal & Sons</h1>
            <p class="text-[10px] md:text-xs tracking-[0.35em] text-gray-600 mt-1 uppercase">Digital Invitations</p>
        </div>

        <div class="envelope-container my-auto flex flex-col items-center">
            <div id="interactiveEnvelope" onclick="openInvitation()" class="animate-float relative w-72 sm:w-96 h-48 sm:h-64 bg-[#c0a283] rounded-sm shadow-2xl cursor-pointer transform transition-transform duration-300 hover:scale-[1.02] border border-[#a88a6d]/50 overflow-hidden flex items-center justify-center">
                <svg class="absolute inset-0 w-full h-full pointer-events-none" viewBox="0 0 384 256" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <polygon points="0,0 384,0 192,135" fill="#b09172" stroke="#997b5d" stroke-width="1.5"/>
                    <polygon points="0,0 0,256 160,128" fill="#b89a7b" opacity="0.9"/>
                    <polygon points="384,0 384,256 224,128" fill="#a88a6c" opacity="0.9"/>
                    <polygon points="0,256 384,256 192,110" fill="#ccac8d" stroke="#a38363" stroke-width="1.5"/>
                    <path d="M 15 240 Q 25 235 30 245 Q 20 250 15 240 Z" fill="#2d4030" opacity="0.7"/>
                    <path d="M 369 240 Q 359 235 354 245 Q 364 250 369 240 Z" fill="#2d4030" opacity="0.7"/>
                </svg>

                <div class="wax-seal relative z-10 w-14 h-14 sm:w-16 sm:h-16 bg-gradient-to-br from-red-700 via-red-800 to-red-950 rounded-full shadow-2xl flex items-center justify-center border-2 border-red-600/40 transform transition-transform group-hover:scale-110">
                    <span class="font-serif italic text-amber-200 text-xl sm:text-2xl font-bold drop-shadow">M</span>
                </div>
            </div>

            <div class="mt-8 text-center animate-pulse">
                <p class="text-xs sm:text-sm font-light tracking-[0.3em] uppercase text-gray-700">Tap to open the invitation</p>
                <div class="w-8 h-[1px] bg-gray-600 mx-auto mt-2"></div>
            </div>
        </div>

        <div class="pb-4 text-[10px] text-gray-500 tracking-widest uppercase">
            Designed for Special Moments
        </div>
    </div>


    <!-- ========================================== -->
    <!-- MAIN INVITATION SITE CONTENT               -->
    <!-- ========================================== -->
    <div id="mainContent" class="opacity-0 transition-opacity duration-1000 flex flex-col min-h-screen">

        <!-- Navigation Bar -->
        <header class="sticky top-0 z-40 bg-cream/90 backdrop-blur-md border-b border-stone-200/60 transition-all duration-300">
            <div class="max-w-6xl mx-auto px-4 sm:px-8 py-4 flex flex-col sm:flex-row items-center justify-between gap-4">
                <a href="#home" class="text-center sm:text-left group">
                    <h2 class="font-serif text-xl tracking-[0.2em] text-gray-800 group-hover:text-gold transition-colors uppercase">Fazal & Sons</h2>
                    <p class="text-[9px] tracking-[0.3em] text-gray-500 uppercase -mt-0.5">Digital Invitations</p>
                </a>

                <nav class="flex items-center space-x-6 sm:space-x-8 text-xs tracking-[0.25em] text-gray-600 uppercase font-medium">
                    <button onclick="navigateTo('home')" id="nav-home" class="nav-link hover:text-gold transition-colors py-1 border-b-2 border-transparent">Home</button>
                    <button onclick="navigateTo('date')" id="nav-date" class="nav-link hover:text-gold transition-colors py-1 border-b-2 border-transparent">Date</button>
                    <button onclick="navigateTo('events')" id="nav-events" class="nav-link hover:text-gold transition-colors py-1 border-b-2 border-transparent">Events</button>
                    <button onclick="navigateTo('wishes')" id="nav-wishes" class="nav-link hover:text-gold transition-colors py-1 border-b-2 border-transparent">Wishes</button>
                </nav>
            </div>
        </header>

        <!-- Main Dynamic View Container -->
        <main class="flex-grow flex flex-col justify-center">

            <!-- --------------------------------------- -->
            <!-- SECTION 1: HOME PAGE (ISLAMIC THEME)    -->
            <!-- --------------------------------------- -->
            <section id="page-home" class="page-section flex py-16 px-4 min-h-[calc(100vh-80px)] bg-cream items-center justify-center">
                <div class="max-w-2xl mx-auto w-full text-center">
                    <div class="bg-white p-8 sm:p-14 rounded-sm shadow-xl border border-stone-200/80 relative">
                        <div class="absolute inset-2 border border-gold/40 pointer-events-none"></div>
                        
                        <!-- Islamic Bismillah Calligraphy Header -->
                        <div class="mb-6 space-y-2">
                            <p class="font-arabic text-2xl sm:text-3xl text-gold font-bold tracking-wide">
                                بِسْمِ ٱللَّٰهِ ٱلرَّحْمَٰنِ ٱلرَّحِيمِ
                            </p>
                            <div class="w-24 h-[1px] bg-gold/50 mx-auto my-3"></div>
                            <!-- Quranic Verse -->
                            <p class="font-arabic text-lg sm:text-xl text-stone-700 leading-relaxed max-w-lg mx-auto font-medium">
                               وَمِنْ آيَاتِهِ أَنْ خَلَقَ لَكُم مِّنْ أَنفُسِكُمْ أَزْوَاجًا
                            </p>
                        </div>

                        <p class="text-xs tracking-[0.3em] uppercase text-gray-500 font-light mb-2">Together With Joy</p>
                        <p class="font-serif italic text-stone-600 text-lg sm:text-xl mb-6">We invite you to our Wedding celebration</p>

                        <h2 class="font-serif text-3xl sm:text-5xl text-gray-900 tracking-wide font-normal mb-2">D/O Fazal Azeem</h2>
                        <div class="font-serif italic text-gold text-2xl my-2">❦</div>
                        <h3 class="font-serif text-2xl sm:text-4xl text-gray-800 tracking-wide font-normal mb-8">Sheikh Junaid</h3>

                        <p class="font-serif italic text-stone-500 text-sm sm:text-base leading-relaxed max-w-md mx-auto mb-10">
                            request the honour of your presence as two families come together in celebration
                        </p>

                        <div class="border-t border-stone-200 pt-6">
                            <button onclick="navigateTo('date')" class="inline-block text-[11px] tracking-[0.35em] text-gray-600 uppercase font-medium hover:text-gold transition-colors group">
                                A Celebration Awaits <span class="inline-block transform transition-transform group-hover:translate-x-1">→</span>
                            </button>
                        </div>
                    </div>
                </div>
            </section>

            <!-- --------------------------------------- -->
            <!-- SECTION 2: DATE & SCRATCH-OFF CARD      -->
            <!-- --------------------------------------- -->
            <section id="page-date" class="page-section hidden py-16 px-4 min-h-[calc(100vh-80px)] bg-cream items-center justify-center">
                <div class="max-w-3xl mx-auto text-center w-full">
                    <p class="text-xs tracking-[0.35em] uppercase text-gray-500 font-light mb-2">Save The Date</p>
                    <h2 class="font-serif text-3xl sm:text-5xl text-gray-900 mb-2">A Day of Celebration</h2>
                    <p class="font-serif italic text-gray-500 text-sm sm:text-base mb-8">Scratch the heart to uncover the day.</p>

                    <div class="relative w-64 h-64 sm:w-80 sm:h-80 mx-auto mb-6 flex items-center justify-center">
                        <div class="absolute inset-0 flex flex-col items-center justify-center bg-amber-50/60 rounded-full border border-amber-200/50 shadow-inner">
                            <span class="text-[10px] tracking-[0.3em] uppercase text-stone-400 mb-1">EVENT STARTING FROM</span>
                            <span class="font-serif text-3xl sm:text-4xl font-normal text-stone-800">DECEMBER</span>
                            <span class="font-serif text-2xl sm:text-3xl text-gold font-light mt-1">26, 2026</span>
                        </div>
                        <canvas id="scratchCanvas" width="320" height="320" class="absolute inset-0 z-10 mx-auto"></canvas>
                    </div>

                    <p class="text-[11px] tracking-[0.3em] uppercase text-gray-500 mb-8">MEHNDI, BARAT, WALIMA</p>
                    <div class="w-48 h-[1px] bg-stone-300 mx-auto mb-10"></div>

                    <button onclick="navigateTo('events')" class="bg-darkGreen text-white text-xs tracking-[0.25em] uppercase px-8 py-3.5 rounded-sm shadow hover:bg-stone-800 transition-colors">
                        View Event Details →
                    </button>
                </div>
            </section>

            <!-- --------------------------------------- -->
            <!-- SECTION 3: EVENTS (MEHNDI, BARAT, WALIMA) -->
            <!-- --------------------------------------- -->
            <section id="page-events" class="page-section hidden py-16 px-4 min-h-[calc(100vh-80px)] bg-darkGreen text-stone-100 items-center justify-center">
                <div class="max-w-4xl mx-auto text-center w-full">
                    <p class="text-xs tracking-[0.35em] uppercase text-goldLight font-light mb-2">The Festivities</p>
                    <h2 class="font-serif text-3xl sm:text-5xl text-white font-normal mb-8">Wedding Events</h2>

                    <div class="flex justify-center items-center gap-3 mb-10 text-xs tracking-[0.2em] uppercase">
                        <a href="#event-mehndi" class="px-4 py-2 border border-goldLight/40 text-goldLight hover:bg-goldLight/10 rounded-sm transition-colors">1. Mehndi</a>
                        <a href="#event-barat" class="px-4 py-2 border border-goldLight/40 text-goldLight hover:bg-goldLight/10 rounded-sm transition-colors">2. Barat</a>
                        <a href="#event-walima" class="px-4 py-2 border border-goldLight/40 text-goldLight hover:bg-goldLight/10 rounded-sm transition-colors">3. Walima</a>
                    </div>

                    <div class="space-y-12 text-center">

                        <!-- EVENT 1: MEHNDI -->
                        <div id="event-mehndi" class="bg-emerald-950/40 p-8 sm:p-10 rounded-sm border border-stone-700/80 shadow-2xl relative">
                            <span class="text-[10px] tracking-[0.3em] uppercase text-goldLight font-medium">Night Function</span>
                            <h3 class="font-serif text-3xl sm:text-5xl text-stone-100 font-light my-2">1. The Mehndi</h3>
                            <p class="text-xs sm:text-sm tracking-[0.25em] text-goldLight uppercase mb-6">25 December 2026</p>
                            
                            <div class="max-w-md mx-auto grid grid-cols-2 border-t border-b border-stone-700/80 py-5 my-6 text-center">
                                <div class="border-r border-stone-700/80 pr-2">
                                    <p class="text-[9px] tracking-[0.25em] text-stone-400 uppercase mb-1">Arrival</p>
                                    <p class="font-serif text-base sm:text-xl text-stone-100">08:00 PM</p>
                                </div>
                                <div class="pl-2">
                                    <p class="text-[9px] tracking-[0.25em] text-stone-400 uppercase mb-1">Mehndi Function</p>
                                    <p class="font-serif text-base sm:text-xl text-stone-100">09:00 PM</p>
                                </div>
                            </div>
                        </div>

                        <!-- EVENT 2: BARAT -->
                        <div id="event-barat" class="bg-emerald-950/40 p-8 sm:p-10 rounded-sm border border-stone-700/80 shadow-2xl relative">
                            <span class="text-[10px] tracking-[0.3em] uppercase text-goldLight font-medium">Main Day Function</span>
                            <h3 class="font-serif text-3xl sm:text-5xl text-stone-100 font-light my-2">2. The Barat</h3>
                            <p class="text-xs sm:text-sm tracking-[0.25em] text-goldLight uppercase mb-3">26 December 2026</p>
                            
                            <p class="text-[11px] sm:text-xs tracking-[0.15em] text-stone-300 uppercase mb-6">
                                Bin Ashraf Banquet Hall, Enforcement station, Sangla Hill
                            </p>

                            <div class="max-w-xl mx-auto grid grid-cols-3 border-t border-b border-stone-700/80 py-5 my-6 text-center">
                                <div>
                                    <p class="text-[9px] tracking-[0.25em] text-stone-400 uppercase mb-1">Arrival</p>
                                    <p class="font-serif text-base sm:text-xl text-stone-100">02:00 PM</p>
                                </div>
                                <div class="border-x border-stone-700/80 px-2">
                                    <p class="text-[9px] tracking-[0.25em] text-stone-400 uppercase mb-1">Nikkah</p>
                                    <p class="font-serif text-base sm:text-xl text-stone-100">03:00 PM</p>
                                </div>
                                <div>
                                    <p class="text-[9px] tracking-[0.25em] text-stone-400 uppercase mb-1">Rukhsati</p>
                                    <p class="font-serif text-base sm:text-xl text-stone-100">05:00 PM</p>
                                </div>
                            </div>
                        </div>

                        <!-- EVENT 3: WALIMA -->
                        <div id="event-walima" class="bg-emerald-950/40 p-8 sm:p-10 rounded-sm border border-stone-700/80 shadow-2xl relative">
                            <span class="text-[10px] tracking-[0.3em] uppercase text-goldLight font-medium">Reception</span>
                            <h3 class="font-serif text-3xl sm:text-5xl text-stone-100 font-light my-2">3. The Walima</h3>
                            <p class="text-xs sm:text-sm tracking-[0.25em] text-goldLight uppercase mb-6">27 December 2026</p>
                            
                            <div class="max-w-xs mx-auto border-t border-b border-stone-700/80 py-5 my-6 text-center">
                                <p class="text-[9px] tracking-[0.25em] text-stone-400 uppercase mb-1">Departure</p>
                                <p class="font-serif text-base sm:text-xl text-stone-100">01:00 PM</p>
                            </div>
                        </div>

                    </div>

                    <div class="flex flex-col sm:flex-row items-center justify-center gap-4 mt-12">
                        <button onclick="openMapModal()" class="inline-flex items-center gap-2 border border-stone-400/60 hover:border-goldLight text-stone-200 hover:text-goldLight text-xs tracking-[0.25em] uppercase px-6 py-3 transition-colors rounded-sm">
                            <i class="fa-solid fa-location-dot"></i>
                            <span>View Location ↗</span>
                        </button>
                        <button onclick="navigateTo('wishes')" class="bg-gold text-stone-900 hover:bg-goldLight font-medium text-xs tracking-[0.25em] uppercase px-6 py-3 transition-colors rounded-sm shadow">
                            Leave A Wish →
                        </button>
                    </div>
                </div>
            </section>

            <!-- --------------------------------------- -->
            <!-- SECTION 4: GUEST WISHES (PRIVATE MODE)  -->
            <!-- --------------------------------------- -->
            <section id="page-wishes" class="page-section hidden py-16 px-4 min-h-[calc(100vh-80px)] bg-cream flex flex-col justify-center">
                <div class="max-w-3xl mx-auto w-full">
                    
                    <div class="text-center mb-10 relative">
                        <p class="text-xs tracking-[0.3em] uppercase text-gray-500 font-light mb-1">Guest Wishes</p>
                        <h2 class="font-serif text-3xl sm:text-5xl text-gray-800">Leave a Wish</h2>
                        <p class="font-serif italic text-gray-500 text-sm sm:text-base mt-2">Send your private heartfelt message directly to Groom & Bride</p>
                        
                        <button onclick="openCoupleAuthModal()" class="mt-4 inline-flex items-center gap-1.5 text-[10px] tracking-[0.2em] uppercase text-gray-400 hover:text-gold transition-colors">
                            <i class="fa-solid fa-lock text-[9px]"></i>
                            <span>Couple Portal</span>
                        </button>
                    </div>

                    <div class="max-w-xl mx-auto w-full">
                        <div class="bg-white p-6 sm:p-10 rounded-sm shadow-xl border border-stone-200">
                            
                            <form id="wishForm" onsubmit="handleWishSubmit(event)" class="space-y-6">
                                <div>
                                    <label class="block text-[10px] tracking-[0.25em] text-gray-500 uppercase mb-2">Your Name</label>
                                    <input type="text" id="guestName" required placeholder="Enter your full name" 
                                           class="w-full px-4 py-3 bg-stone-50/50 border border-stone-300 rounded-sm text-sm focus:outline-none focus:border-darkGreen transition-colors">
                                </div>

                                <div>
                                    <label class="block text-[10px] tracking-[0.25em] text-gray-500 uppercase mb-2">Your Wish</label>
                                    <textarea id="guestWish" rows="5" required placeholder="Write your warm blessings and wishes for the couple..." 
                                              class="w-full px-4 py-3 bg-stone-50/50 border border-stone-300 rounded-sm text-sm focus:outline-none focus:border-darkGreen transition-colors resize-none"></textarea>
                                </div>

                                <button type="submit" class="w-full bg-darkGreen hover:bg-stone-800 text-white font-sans text-xs tracking-[0.25em] uppercase py-4 transition-all duration-300 rounded-sm shadow-md">
                                    Send Private Wish
                                </button>
                            </form>

                            <div id="thankYouMsg" class="hidden text-center py-8 space-y-4">
                                <div class="w-12 h-12 bg-amber-50 rounded-full flex items-center justify-center mx-auto text-gold text-2xl border border-amber-200">
                                    ❦
                                </div>
                                <h3 class="font-serif text-2xl text-gray-800">Thank You!</h3>
                                <p class="font-serif italic text-gray-600 text-base max-w-md mx-auto leading-relaxed">
                                    Your wish has been securely recorded and sent to the couple. Thank you for sharing in our joy!
                                </p>
                                <button onclick="resetWishForm()" class="mt-4 text-[10px] tracking-[0.2em] uppercase text-gray-400 hover:text-darkGreen underline underline-offset-4">
                                    Send Another Wish
                                </button>
                            </div>

                        </div>
                    </div>

                </div>
            </section>

        </main>

        <!-- Footer -->
        <footer class="bg-cream border-t border-stone-200/60 py-6 text-center text-gray-400 text-[10px] tracking-[0.25em] uppercase">
            <p>Fazal Mojeeb & Family • 2026 Digital Invitation</p>
        </footer>
    </div>


    <!-- ========================================== -->
    <!-- COUPLE / ADMIN ACCESS MODAL                -->
    <!-- ========================================== -->
    <div id="coupleModal" class="fixed inset-0 z-50 bg-black/60 backdrop-blur-sm flex items-center justify-center p-4 opacity-0 pointer-events-none transition-opacity duration-300">
        <div class="bg-white rounded-sm shadow-2xl w-full max-w-xl overflow-hidden flex flex-col">
            <div class="p-4 bg-darkGreen text-white flex items-center justify-between">
                <div class="flex items-center gap-2">
                    <i class="fa-solid fa-heart text-gold text-sm"></i>
                    <h3 class="font-serif text-lg tracking-wider">Couple's Private Dashboard</h3>
                </div>
                <button onclick="closeCoupleModal()" class="text-stone-300 hover:text-white p-1">
                    <i class="fa-solid fa-xmark text-lg"></i>
                </button>
            </div>

            <div id="adminAuthView" class="p-8 text-center">
                <p class="text-xs tracking-[0.25em] text-gray-500 uppercase mb-4">Enter Passcode to View Wishes</p>
                <div class="max-w-xs mx-auto space-y-4">
                    <input type="password" id="couplePasscode" placeholder="Passcode (Default: 1234)" 
                           class="w-full px-4 py-3 text-center tracking-[0.3em] font-bold bg-stone-50 border border-stone-300 rounded-sm text-sm focus:outline-none focus:border-darkGreen">
                    <p id="passcodeError" class="text-xs text-red-500 hidden">Incorrect passcode. Try 1234.</p>
                    <button onclick="verifyPasscode()" class="w-full bg-darkGreen hover:bg-stone-800 text-white font-sans text-xs tracking-[0.25em] uppercase py-3 transition-colors rounded-sm">
                        Unlock Wishes
                    </button>
                </div>
            </div>

            <div id="adminWishesView" class="hidden p-6 bg-stone-50 max-h-[60vh] overflow-y-auto space-y-4 custom-scrollbar">
                <div class="flex items-center justify-between border-b border-stone-200 pb-3">
                    <span id="wishesCount" class="text-xs tracking-[0.2em] uppercase text-gray-500">Received Wishes</span>
                    <button onclick="logoutAdmin()" class="text-[10px] tracking-[0.2em] uppercase text-red-600 hover:underline">Lock Dashboard</button>
                </div>
                <div id="adminWishesContainer" class="space-y-4">
                </div>
            </div>

            <div class="p-4 border-t border-stone-200 bg-white flex justify-end">
                <button onclick="closeCoupleModal()" class="px-6 py-2 bg-stone-200 hover:bg-stone-300 text-gray-800 text-xs tracking-widest uppercase transition-colors rounded-sm">Close</button>
            </div>
        </div>
    </div>


    <!-- ========================================== -->
    <!-- GOOGLE MAP LOCATION MODAL                  -->
    <!-- ========================================== -->
    <div id="mapModal" class="fixed inset-0 z-50 bg-black/60 backdrop-blur-sm flex items-center justify-center p-4 opacity-0 pointer-events-none transition-opacity duration-300">
        <div class="bg-white rounded-sm shadow-2xl w-full max-w-2xl overflow-hidden flex flex-col">
            <div class="p-4 bg-darkGreen text-white flex items-center justify-between">
                <h3 class="font-serif text-lg tracking-wider">Venue Location</h3>
                <button onclick="closeMapModal()" class="text-stone-300 hover:text-white p-1">
                    <i class="fa-solid fa-xmark text-lg"></i>
                </button>
            </div>
            <div class="p-4 bg-stone-50">
                <p class="text-xs font-semibold text-gray-800 uppercase tracking-wider mb-1">Bin Ashraf Banquet Hall</p>
                <p class="text-xs text-gray-600 mb-4">Bin Ashraf Banquet Hall, Enforcement station, Sangla Hill</p>
                
                <div class="w-full h-72 bg-stone-200 rounded overflow-hidden">
                    <iframe 
                        title="Venue Location Map"
                        class="w-full h-full border-0" 
                        loading="lazy" 
                        allowfullscreen 
                        referrerpolicy="no-referrer-when-downgrade"
                        src="https://www.google.com/maps/search/31.714021,+73.385533?entry=tts&g_ep=EgoyMDI2MTEwMS4wIPu8ASoASAFQAw%3D%3D&skid=c3c55740-0373-4dac-92c2-12c1afa5e1d9">
                    </iframe>
                </div>
            </div>
            <div class="p-4 border-t border-stone-200 flex justify-end">
                <button onclick="closeMapModal()" class="px-6 py-2 bg-stone-200 hover:bg-stone-300 text-gray-800 text-xs tracking-widest uppercase transition-colors rounded-sm">Close</button>
            </div>
        </div>
    </div>


    <!-- ========================================== -->
    <!-- JAVASCRIPT CONTROLLERS                     -->
    <!-- ========================================== -->
    <script>
        function openInvitation() {
            const overlay = document.getElementById('envelopeOverlay');
            const mainContent = document.getElementById('mainContent');
            const envelope = document.getElementById('interactiveEnvelope');

            envelope.style.transform = 'scale(0.95)';
            
            setTimeout(() => {
                overlay.classList.add('opacity-0', 'pointer-events-none');
                mainContent.classList.remove('opacity-0');
                initScratchCard();
            }, 400);
        }

        function navigateTo(pageId) {
            const pages = ['home', 'date', 'events', 'wishes'];
            
            pages.forEach(p => {
                const pageEl = document.getElementById(`page-${p}`);
                const navEl = document.getElementById(`nav-${p}`);
                
                if (pageEl) {
                    if (p === pageId) {
                        pageEl.classList.remove('hidden');
                        pageEl.classList.add('flex');
                    } else {
                        pageEl.classList.add('hidden');
                        pageEl.classList.remove('flex');
                    }
                }

                if (navEl) {
                    if (p === pageId) {
                        navEl.classList.add('text-gold', 'border-gold');
                        navEl.classList.remove('border-transparent');
                    } else {
                        navEl.classList.remove('text-gold', 'border-gold');
                        navEl.classList.add('border-transparent');
                    }
                }
            });

            window.scrollTo({ top: 0, behavior: 'smooth' });
        }

        function initScratchCard() {
            const canvas = document.getElementById('scratchCanvas');
            if(!canvas) return;
            
            const ctx = canvas.getContext('2d');
            const width = canvas.width;
            const height = canvas.height;

            ctx.save();
            ctx.beginPath();
            ctx.moveTo(width / 2, height / 5 * 4);
            ctx.bezierCurveTo(width / 2, height / 5 * 3.8, width / 6, height / 2, width / 6, height / 3);
            ctx.bezierCurveTo(width / 6, height / 8, width / 2.5, height / 8, width / 2, height / 3.5);
            ctx.bezierCurveTo(width / 1.6, height / 8, width / 1.16, height / 8, width / 1.16, height / 3);
            ctx.bezierCurveTo(width / 1.16, height / 2, width / 2, height / 5 * 3.8, width / 2, height / 5 * 4);
            ctx.closePath();
            ctx.clip();

            const grad = ctx.createLinearGradient(0, 0, width, height);
            grad.addColorStop(0, '#f4a5b8');
            grad.addColorStop(0.5, '#e58c9e');
            grad.addColorStop(1, '#d07c8c');
            ctx.fillStyle = grad;
            ctx.fillRect(0, 0, width, height);

            ctx.font = '12px Montserrat';
            ctx.fillStyle = '#ffffff';
            ctx.textAlign = 'center';
            ctx.fillText('SCRATCH HERE', width / 2, height / 2.1);
            
            ctx.font = '18px serif';
            ctx.fillText('♥', width / 2, height / 1.7);
            
            ctx.restore();

            let isDrawing = false;

            function scratch(e) {
                if (!isDrawing) return;
                const rect = canvas.getBoundingClientRect();
                const x = (e.clientX || (e.touches && e.touches[0].clientX)) - rect.left;
                const y = (e.clientY || (e.touches && e.touches[0].clientY)) - rect.top;

                ctx.globalCompositeOperation = 'destination-out';
                ctx.beginPath();
                ctx.arc(x, y, 22, 0, Math.PI * 2);
                ctx.fill();
            }

            canvas.addEventListener('mousedown', (e) => { isDrawing = true; scratch(e); });
            canvas.addEventListener('mousemove', scratch);
            window.addEventListener('mouseup', () => { isDrawing = false; });

            canvas.addEventListener('touchstart', (e) => { isDrawing = true; scratch(e); e.preventDefault(); });
            canvas.addEventListener('touchmove', (e) => { scratch(e); e.preventDefault(); });
            window.addEventListener('touchend', () => { isDrawing = false; });
        }

        async function handleWishSubmit(event) {
            event.preventDefault();
            const nameInput = document.getElementById('guestName');
            const wishInput = document.getElementById('guestWish');
            const formEl = document.getElementById('wishForm');
            const thankYouEl = document.getElementById('thankYouMsg');
            const submitBtn = event.target.querySelector('button[type="submit"]');

            const name = nameInput.value.trim();
            const wish = wishInput.value.trim();

            if (!name || !wish) return;

            try {
                submitBtn.disabled = true;
                submitBtn.innerText = "RECORDING PRIVATELY...";

                if (window.saveWishToCloud) {
                    await window.saveWishToCloud(name, wish);
                }

                formEl.classList.add('hidden');
                thankYouEl.classList.remove('hidden');

                nameInput.value = '';
                wishInput.value = '';
            } catch (err) {
                console.error("Failed to send wish:", err);
            } finally {
                submitBtn.disabled = false;
                submitBtn.innerText = "SEND PRIVATE WISH";
            }
        }

        function resetWishForm() {
            document.getElementById('wishForm').classList.remove('hidden');
            document.getElementById('thankYouMsg').classList.add('hidden');
        }

        window.isAdminLoggedIn = false;

        function openCoupleAuthModal() {
            const modal = document.getElementById('coupleModal');
            modal.classList.remove('opacity-0', 'pointer-events-none');
        }

        function closeCoupleModal() {
            const modal = document.getElementById('coupleModal');
            modal.classList.add('opacity-0', 'pointer-events-none');
        }

        function verifyPasscode() {
            const input = document.getElementById('couplePasscode');
            const err = document.getElementById('passcodeError');
            
            if (input.value === '1234') {
                window.isAdminLoggedIn = true;
                err.classList.add('hidden');
                input.value = '';
                document.getElementById('adminAuthView').classList.add('hidden');
                document.getElementById('adminWishesView').classList.remove('hidden');
                renderAdminWishes();
            } else {
                err.classList.remove('hidden');
            }
        }

        function logoutAdmin() {
            window.isAdminLoggedIn = false;
            document.getElementById('adminAuthView').classList.remove('hidden');
            document.getElementById('adminWishesView').classList.add('hidden');
        }

        function renderAdminWishes() {
            const container = document.getElementById('adminWishesContainer');
            const countLabel = document.getElementById('wishesCount');
            const list = window.allWishesList || [];

            countLabel.innerText = `Received Wishes (${list.length})`;

            if (list.length === 0) {
                container.innerHTML = `
                    <div class="bg-white p-6 rounded text-center border border-stone-200">
                        <p class="font-serif italic text-gray-500 text-sm">No private wishes received yet.</p>
                    </div>
                `;
                return;
            }

            container.innerHTML = list.map(w => `
                <div class="bg-white p-5 rounded-sm border border-stone-200 shadow-sm space-y-2">
                    <div class="flex items-center justify-between border-b border-stone-100 pb-2">
                        <span class="font-medium text-gray-900 text-xs sm:text-sm uppercase tracking-wider">${escapeHtml(w.name)}</span>
                        <span class="text-[10px] text-gray-400 tracking-widest">${w.date || ''}</span>
                    </div>
                    <p class="font-serif italic text-gray-700 text-sm leading-relaxed">“${escapeHtml(w.wish)}”</p>
                </div>
            `).join('');
        }

        function escapeHtml(text) {
            return text
                .replace(/&/g, "&amp;")
                .replace(/</g, "&lt;")
                .replace(/>/g, "&gt;")
                .replace(/"/g, "&quot;")
                .replace(/'/g, "&#039;");
        }

        function openMapModal() {
            const modal = document.getElementById('mapModal');
            modal.classList.remove('opacity-0', 'pointer-events-none');
        }

        function closeMapModal() {
            const modal = document.getElementById('mapModal');
            modal.classList.add('opacity-0', 'pointer-events-none');
        }

        window.addEventListener('DOMContentLoaded', () => {
            document.getElementById('nav-home').classList.add('text-gold', 'border-gold');
        });
    </script>
</body>
</html>
