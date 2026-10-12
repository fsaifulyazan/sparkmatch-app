# sparkmatch-app
​A modern Telegram Dating Mini App functions like Tinder or Bumble inside Telegram
```html
<!DOCTYPE html>
<html lang="en" class="dark h-full">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>SparkMatch - Telegram Dating Mini App</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        telegram: {
                            bg: '#17212b',
                            card: '#242f3d',
                            accent: '#0088cc',
                            hover: '#1c2733',
                            star: '#f5c518',
                            pink: '#ff4b72',
                            purple: '#9d4edd'
                        }
                    },
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                    }
                }
            }
        }
    </script>
    <!-- Lucide Icons & FontAwesome -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Inter Font -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    
    <style>
        /* Custom Styles & Swipe Animations */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #0e1621;
            color: #f5f5f5;
            user-select: none;
            -webkit-user-select: none;
            overflow: hidden;
        }

        .swipe-card {
            touch-action: none;
            transition: transform 0.2s ease, opacity 0.2s ease;
            will-change: transform, opacity;
        }

        .swipe-card.swiping {
            transition: none;
        }

        .badge-like {
            border: 3px solid #10B981;
            color: #10B981;
            transform: rotate(-15deg);
        }

        .badge-nope {
            border: 3px solid #EF4444;
            color: #EF4444;
            transform: rotate(15deg);
        }

        .badge-super {
            border: 3px solid #3B82F6;
            color: #3B82F6;
            transform: rotate(0deg);
        }

        /* Custom Scrollbars */
        ::-webkit-scrollbar {
            width: 4px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(0, 0, 0, 0.1);
        }
        ::-webkit-scrollbar-thumb {
            background: rgba(255, 255, 255, 0.2);
            border-radius: 4px;
        }

        /* Glow effects */
        .glow-pink {
            box-shadow: 0 0 20px rgba(255, 75, 114, 0.4);
        }
        .glow-star {
            box-shadow: 0 0 15px rgba(245, 197, 24, 0.4);
        }

        @keyframes pulse-ring {
            0% { transform: scale(0.95); opacity: 0.8; }
            50% { transform: scale(1.05); opacity: 1; }
            100% { transform: scale(0.95); opacity: 0.8; }
        }
        .pulse-animation {
            animation: pulse-ring 2s infinite ease-in-out;
        }
    </style>
</head>
<body class="h-full w-full flex flex-col justify-between max-w-md mx-auto relative shadow-2xl border-x border-gray-800">

    <!-- TELEGRAM WEBAPP SIMULATION HEADER -->
    <header class="bg-[#17212b] px-4 py-2.5 flex items-center justify-between border-b border-gray-800 z-40 shrink-0">
        <div class="flex items-center gap-2.5">
            <div class="relative">
                <img id="userHeaderAvatar" src="https://images.unsplash.com/photo-1534528741775-53994a69daeb?auto=format&fit=crop&q=80&w=150" class="w-9 h-9 rounded-full object-cover border-2 border-telegram-accent" alt="User">
                <span class="absolute bottom-0 right-0 w-2.5 h-2.5 bg-green-500 rounded-full border-2 border-[#17212b]"></span>
            </div>
            <div>
                <div class="flex items-center gap-1">
                    <span id="userHeaderName" class="font-bold text-xs text-white">Faiz</span>
                    <span id="userVipBadge" class="hidden text-[10px] bg-gradient-to-r from-amber-400 to-amber-600 text-black px-1.5 py-0.2 rounded-full font-black uppercase tracking-wider">VIP</span>
                </div>
                <p class="text-[10px] text-gray-400">@faiz_dev <span class="text-telegram-accent">● Online</span></p>
            </div>
        </div>

        <!-- TELEGRAM STARS BALANCE COUNTER -->
        <button onclick="switchTab('shop')" class="flex items-center gap-1.5 bg-[#242f3d] hover:bg-[#2c3a4b] px-3 py-1.5 rounded-full border border-yellow-500/30 transition-all duration-200">
            <i class="fa-solid font-black fa-star text-yellow-400 text-xs animate-bounce"></i>
            <span id="starsBalance" class="text-xs font-bold text-yellow-400">250</span>
            <span class="text-[10px] text-gray-400 font-medium">Stars</span>
            <i class="fa-solid fa-plus text-[10px] bg-yellow-400/20 text-yellow-400 p-1 rounded-full ml-0.5"></i>
        </button>
    </header>

    <!-- MAIN VIEW CONTAINER -->
    <main class="flex-1 relative overflow-hidden bg-[#0e1621] flex flex-col">

        <!-- 1. DISCOVER / SWIPE TAB -->
        <section id="tab-discover" class="tab-content flex-1 flex flex-col p-3 relative h-full">
            
            <!-- SWIPE CARDS CONTAINER -->
            <div id="cardDeck" class="relative flex-1 w-full h-full flex items-center justify-center">
                <!-- Swipe Cards populated by JS -->
            </div>

            <!-- ACTION BUTTONS TOOLBAR -->
            <div class="flex items-center justify-evenly py-3 px-2 z-30 shrink-0">
                <!-- Rewind -->
                <button onclick="rewindSwipe()" class="w-11 h-11 rounded-full bg-[#242f3d] text-yellow-400 flex items-center justify-center shadow-lg hover:scale-110 active:scale-95 transition border border-gray-700">
                    <i class="fa-solid fa-[#242f3d] fa-rotate-left text-lg"></i>
                </button>
                <!-- Dislike (Pass) -->
                <button onclick="handleSwipe('left')" class="w-14 h-14 rounded-full bg-[#242f3d] text-red-500 flex items-center justify-center shadow-lg hover:scale-110 active:scale-95 transition border border-red-500/30">
                    <i class="fa-solid fa-xmark text-2xl"></i>
                </button>
                <!-- Super Like -->
                <button onclick="handleSwipe('up')" class="w-11 h-11 rounded-full bg-[#242f3d] text-blue-400 flex items-center justify-center shadow-lg hover:scale-110 active:scale-95 transition border border-blue-400/30">
                    <i class="fa-solid fa-star text-lg"></i>
                </button>
                <!-- Like -->
                <button onclick="handleSwipe('right')" class="w-14 h-14 rounded-full bg-gradient-to-r from-pink-500 to-rose-600 text-white flex items-center justify-center shadow-lg glow-pink hover:scale-110 active:scale-95 transition">
                    <i class="fa-solid fa-heart text-2xl"></i>
                </button>
                <!-- Boost Profile -->
                <button onclick="openBoostModal()" class="w-11 h-11 rounded-full bg-[#242f3d] text-purple-400 flex items-center justify-center shadow-lg hover:scale-110 active:scale-95 transition border border-purple-500/30">
                    <i class="fa-solid fa-bolt text-lg"></i>
                </button>
            </div>
        </section>

        <!-- 2. MATCHES & CHAT TAB -->
        <section id="tab-chat" class="tab-content hidden flex-1 flex-col h-full bg-[#0e1621]">
            <!-- Matches Bar -->
            <div class="p-3 border-b border-gray-800">
                <h3 class="text-xs font-semibold text-gray-400 uppercase tracking-wider mb-2">New Matches</h3>
                <div id="matchesHorizontalList" class="flex gap-3 overflow-x-auto pb-1">
                    <!-- Dynamic Matches list -->
                </div>
            </div>

            <!-- Active Chats List View -->
            <div id="chatListContainer" class="flex-1 overflow-y-auto">
                <div class="px-3 py-2 text-xs font-semibold text-gray-400 uppercase tracking-wider">Messages</div>
                <div id="chatsList" class="divide-y divide-gray-800/50">
                    <!-- Dynamic Chats -->
                </div>
            </div>

            <!-- Single Active Direct Chat Overlay Screen -->
            <div id="activeChatScreen" class="hidden absolute inset-0 bg-[#0e1621] z-50 flex flex-col">
                <!-- Chat Header -->
                <div class="p-3 bg-[#17212b] border-b border-gray-800 flex items-center justify-between">
                    <div class="flex items-center gap-3">
                        <button onclick="closeChatScreen()" class="text-gray-400 hover:text-white p-1">
                            <i class="fa-solid fa-arrow-left text-lg"></i>
                        </button>
                        <img id="chatHeaderAvatar" src="" class="w-9 h-9 rounded-full object-cover">
                        <div>
                            <h4 id="chatHeaderName" class="font-bold text-sm text-white">Match Name</h4>
                            <span class="text-[10px] text-green-400">Online</span>
                        </div>
                    </div>
                    <button onclick="triggerTelegramCall()" class="w-8 h-8 rounded-full bg-[#242f3d] text-telegram-accent flex items-center justify-center">
                        <i class="fa-solid fa-phone text-xs"></i>
                    </button>
                </div>

                <!-- Chat Messages Body -->
                <div id="chatMessages" class="flex-1 p-4 overflow-y-auto flex flex-col gap-3">
                    <!-- Dynamic Message Bubbles -->
                </div>

                <!-- Quick Icebreakers -->
                <div class="p-2 bg-[#17212b]/50 border-t border-gray-800 flex gap-2 overflow-x-auto text-xs">
                    <button onclick="sendQuickMessage('✨ Hey! What\'s your favorite coffee spot?')" class="bg-[#242f3d] text-gray-300 px-3 py-1 rounded-full whitespace-nowrap hover:border-telegram-accent border border-transparent">☕ Coffee spot?</button>
                    <button onclick="sendQuickMessage('✈️ What is your dream travel destination?')" class="bg-[#242f3d] text-gray-300 px-3 py-1 rounded-full whitespace-nowrap hover:border-telegram-accent border border-transparent">✈️ Travel spot?</button>
                    <button onclick="sendQuickMessage('🎵 What song is on repeat for you right now?')" class="bg-[#242f3d] text-gray-300 px-3 py-1 rounded-full whitespace-nowrap hover:border-telegram-accent border border-transparent">🎵 Music vibe?</button>
                </div>

                <!-- Chat Input Bar -->
                <div class="p-3 bg-[#17212b] border-t border-gray-800 flex items-center gap-2">
                    <input type="text" id="chatInput" placeholder="Write a message..." class="flex-1 bg-[#242f3d] text-white text-sm rounded-full px-4 py-2 focus:outline-none focus:ring-1 focus:ring-telegram-accent">
                    <button onclick="sendChatMessage()" class="w-9 h-9 bg-telegram-accent text-white rounded-full flex items-center justify-center hover:bg-blue-600 transition">
                        <i class="fa-solid fa-paper-plane text-sm"></i>
                    </button>
                </div>
            </div>
        </section>

        <!-- 3. MONETIZATION / SHOP TAB -->
        <section id="tab-shop" class="tab-content hidden flex-1 overflow-y-auto p-4 space-y-4">
            <!-- Header Banner -->
            <div class="bg-gradient-to-r from-purple-900/60 via-telegram-card to-pink-900/60 p-4 rounded-2xl border border-purple-500/30 text-center relative overflow-hidden">
                <div class="absolute -right-4 -bottom-4 text-6xl opacity-10 text-yellow-400">
                    <i class="fa-solid fa-star"></i>
                </div>
                <span class="bg-yellow-400/20 text-yellow-400 text-[10px] font-bold px-2 py-0.5 rounded-full uppercase tracking-wider">SparkMatch VIP Store</span>
                <h2 class="text-xl font-extrabold text-white mt-1">Get 10x More Matches</h2>
                <p class="text-xs text-gray-300 mt-1">Unlock premium perks using native Telegram Stars (XTR)</p>
            </div>

            <!-- VIP Membership Offer -->
            <div class="bg-[#242f3d] p-4 rounded-2xl border border-amber-500/30 relative">
                <div class="flex justify-between items-start mb-2">
                    <div>
                        <span class="text-xs text-amber-400 font-bold tracking-widest uppercase">Popular Choice</span>
                        <h3 class="text-lg font-bold text-white">Spark VIP Gold Pass</h3>
                    </div>
                    <span class="bg-amber-500 text-black font-black text-xs px-2 py-1 rounded-md">SAVE 40%</span>
                </div>
                <ul class="text-xs text-gray-300 space-y-2 mb-4">
                    <li class="flex items-center gap-2"><i class="fa-solid fa-circle-check text-green-400"></i> Unlimited Daily Swipes</li>
                    <li class="flex items-center gap-2"><i class="fa-solid fa-circle-check text-green-400"></i> See Who Liked You First</li>
                    <li class="flex items-center gap-2"><i class="fa-solid fa-circle-check text-green-400"></i> 5 Free Super Likes per day</li>
                    <li class="flex items-center gap-2"><i class="fa-solid fa-circle-check text-green-400"></i> Unlimited Rewinds & Location Teleport</li>
                </ul>
                <button onclick="purchaseProduct('Spark VIP Pass', 150)" class="w-full py-2.5 rounded-xl bg-gradient-to-r from-amber-400 to-amber-600 text-black font-bold text-xs flex items-center justify-center gap-2 shadow-lg glow-star">
                    <i class="fa-solid fa-star"></i> Unlock for 150 Stars / month
                </button>
            </div>

            <!-- Individual Microtransactions Grid -->
            <div class="grid grid-cols-2 gap-3">
                <!-- Pack 1: Super Likes -->
                <div class="bg-[#242f3d] p-3 rounded-xl border border-gray-700 flex flex-col justify-between">
                    <div>
                        <div class="w-8 h-8 rounded-lg bg-blue-500/20 text-blue-400 flex items-center justify-center mb-2">
                            <i class="fa-solid fa-star"></i>
                        </div>
                        <h4 class="font-bold text-sm text-white">5x Super Likes</h4>
                        <p class="text-[10px] text-gray-400 mt-0.5">3x higher chance of match</p>
                    </div>
                    <button onclick="purchaseProduct('5x Super Likes', 50)" class="mt-3 w-full py-1.5 bg-[#17212b] hover:bg-telegram-accent text-yellow-400 hover:text-white font-semibold text-xs rounded-lg flex items-center justify-center gap-1 border border-yellow-500/30 transition">
                        <i class="fa-solid fa-star text-[10px]"></i> 50 Stars
                    </button>
                </div>

                <!-- Pack 2: Profile Boost -->
                <div class="bg-[#242f3d] p-3 rounded-xl border border-gray-700 flex flex-col justify-between">
                    <div>
                        <div class="w-8 h-8 rounded-lg bg-purple-500/20 text-purple-400 flex items-center justify-center mb-2">
                            <i class="fa-solid fa-bolt"></i>
                        </div>
                        <h4 class="font-bold text-sm text-white">30 Min Spotlight</h4>
                        <p class="text-[10px] text-gray-400 mt-0.5">Be top profile in your area</p>
                    </div>
                    <button onclick="purchaseProduct('30 Min Spotlight Boost', 75)" class="mt-3 w-full py-1.5 bg-[#17212b] hover:bg-telegram-accent text-yellow-400 hover:text-white font-semibold text-xs rounded-lg flex items-center justify-center gap-1 border border-yellow-500/30 transition">
                        <i class="fa-solid fa-star text-[10px]"></i> 75 Stars
                    </button>
                </div>
            </div>

            <!-- REWARDED ADS SECTION (INCOME GENERATOR) -->
            <div class="bg-gradient-to-r from-emerald-900/40 to-teal-900/40 p-4 rounded-2xl border border-emerald-500/30 flex items-center justify-between">
                <div class="space-y-1">
                    <span class="bg-emerald-500/20 text-emerald-400 text-[10px] font-bold px-2 py-0.5 rounded-full">Free Reward</span>
                    <h4 class="font-bold text-sm text-white">Watch Ad for +5 Swipes</h4>
                    <p class="text-[10px] text-gray-300">Monetag / Adsgram Sponsored Video</p>
                </div>
                <button onclick="watchRewardedAd()" class="px-3 py-2 bg-emerald-500 hover:bg-emerald-600 text-black font-bold text-xs rounded-xl shadow-md transition">
                    Watch Ad
                </button>
            </div>
        </section>

        <!-- 4. PROFILE & EDIT TAB -->
        <section id="tab-profile" class="tab-content hidden flex-1 overflow-y-auto p-4 space-y-4">
            <!-- Profile Header Card -->
            <div class="bg-[#242f3d] p-4 rounded-2xl border border-gray-800 text-center relative">
                <div class="relative inline-block">
                    <img id="userEditAvatar" src="https://images.unsplash.com/photo-1534528741775-53994a69daeb?auto=format&fit=crop&q=80&w=300" class="w-20 h-20 rounded-full object-cover border-4 border-telegram-accent mx-auto">
                    <button onclick="triggerPhotoChange()" class="absolute bottom-0 right-0 w-7 h-7 bg-telegram-accent text-white rounded-full flex items-center justify-center text-xs shadow-md">
                        <i class="fa-solid fa-camera"></i>
                    </button>
                </div>
                <h3 class="text-base font-bold text-white mt-2 flex items-center justify-center gap-1">
                    <span id="userProfileNameDisplay">Faiz</span>, <span id="userAgeDisplay">26</span>
                </h3>
                <p id="userBioDisplay" class="text-xs text-gray-300 mt-1 italic">"Full-stack telegram dev building the future of social apps. 🚀 Coffee lover."</p>

                <!-- Stats Bar -->
                <div class="grid grid-cols-3 gap-2 mt-4 pt-3 border-t border-gray-700 text-center">
                    <div>
                        <div class="text-sm font-bold text-white" id="statLikesCount">128</div>
                        <div class="text-[10px] text-gray-400">Likes Recd</div>
                    </div>
                    <div>
                        <div class="text-sm font-bold text-telegram-accent" id="statMatchesCount">14</div>
                        <div class="text-[10px] text-gray-400">Matches</div>
                    </div>
                    <div>
                        <div class="text-sm font-bold text-yellow-400" id="statStarsSpent">350</div>
                        <div class="text-[10px] text-gray-400">Stars Earned</div>
                    </div>
                </div>
            </div>

            <!-- EDIT PROFILE FORM -->
            <div class="bg-[#242f3d] p-4 rounded-2xl border border-gray-800 space-y-3">
                <h4 class="text-xs font-bold text-gray-400 uppercase tracking-wider">Edit Profile Details</h4>
                
                <div>
                    <label class="text-[11px] text-gray-300 block mb-1">Display Bio</label>
                    <textarea id="inputBio" rows="2" class="w-full bg-[#17212b] border border-gray-700 rounded-xl p-2.5 text-xs text-white focus:outline-none focus:border-telegram-accent">Full-stack telegram dev building the future of social apps. 🚀 Coffee lover.</textarea>
                </div>

                <div class="grid grid-cols-2 gap-2">
                    <div>
                        <label class="text-[11px] text-gray-300 block mb-1">Age</label>
                        <input type="number" id="inputAge" value="26" class="w-full bg-[#17212b] border border-gray-700 rounded-xl p-2 text-xs text-white">
                    </div>
                    <div>
                        <label class="text-[11px] text-gray-300 block mb-1">Interested In</label>
                        <select id="inputLookingFor" class="w-full bg-[#17212b] border border-gray-700 rounded-xl p-2 text-xs text-white">
                            <option value="Women">Women</option>
                            <option value="Men">Men</option>
                            <option value="Everyone">Everyone</option>
                        </select>
                    </div>
                </div>

                <div>
                    <label class="text-[11px] text-gray-300 block mb-1">Interests & Tags</label>
                    <div id="interestsContainer" class="flex flex-wrap gap-1.5">
                        <span class="text-[10px] bg-telegram-accent/20 text-telegram-accent px-2 py-1 rounded-full border border-telegram-accent/30">💻 Coding</span>
                        <span class="text-[10px] bg-telegram-accent/20 text-telegram-accent px-2 py-1 rounded-full border border-telegram-accent/30">☕ Coffee</span>
                        <span class="text-[10px] bg-telegram-accent/20 text-telegram-accent px-2 py-1 rounded-full border border-telegram-accent/30">🎮 Gaming</span>
                        <span class="text-[10px] bg-telegram-accent/20 text-telegram-accent px-2 py-1 rounded-full border border-telegram-accent/30">✈️ Travel</span>
                    </div>
                </div>

                <button onclick="saveProfileData()" class="w-full py-2 bg-telegram-accent hover:bg-blue-600 text-white font-bold text-xs rounded-xl transition">
                    Save Changes
                </button>
            </div>

            <!-- REFERRAL PROGRAM CARD -->
            <div class="bg-gradient-to-r from-blue-900/40 to-indigo-900/40 p-4 rounded-2xl border border-blue-500/30 space-y-2">
                <div class="flex items-center gap-2">
                    <i class="fa-solid fa-gift text-yellow-400 text-base"></i>
                    <h4 class="font-bold text-sm text-white">Invite Friends & Earn Stars</h4>
                </div>
                <p class="text-xs text-gray-300">Get 50 Telegram Stars for every friend who joins SparkMatch!</p>
                <div class="flex gap-2">
                    <input type="text" readonly value="https://t.me/SparkMatchBot?start=ref_faiz" class="flex-1 bg-[#17212b] border border-gray-700 text-[10px] text-gray-300 rounded-lg px-2 py-1.5">
                    <button onclick="copyReferralLink()" class="px-3 py-1.5 bg-telegram-accent text-white font-bold text-xs rounded-lg hover:bg-blue-600">
                        Copy
                    </button>
                </div>
            </div>
        </section>

    </main>

    <!-- BOTTOM TAB NAVIGATION BAR -->
    <nav class="bg-[#17212b] border-t border-gray-800 px-6 py-2 flex justify-between items-center z-40 shrink-0">
        <!-- Discover -->
        <button onclick="switchTab('discover')" id="nav-discover" class="nav-btn text-telegram-accent flex flex-col items-center gap-1">
            <i class="fa-solid fa-fire text-lg"></i>
            <span class="text-[10px] font-medium">Discover</span>
        </button>
        <!-- Chats -->
        <button onclick="switchTab('chat')" id="nav-chat" class="nav-btn text-gray-400 hover:text-white flex flex-col items-center gap-1 relative">
            <i class="fa-solid fa-comments text-lg"></i>
            <span class="text-[10px] font-medium">Chats</span>
            <span id="chatUnreadDot" class="absolute top-0 right-2 w-2 h-2 bg-pink-500 rounded-full"></span>
        </button>
        <!-- Stars Shop -->
        <button onclick="switchTab('shop')" id="nav-shop" class="nav-btn text-gray-400 hover:text-white flex flex-col items-center gap-1">
            <i class="fa-solid fa-store text-lg"></i>
            <span class="text-[10px] font-medium">Stars Shop</span>
        </button>
        <!-- Profile -->
        <button onclick="switchTab('profile')" id="nav-profile" class="nav-btn text-gray-400 hover:text-white flex flex-col items-center gap-1">
            <i class="fa-solid fa-user text-lg"></i>
            <span class="text-[10px] font-medium">Profile</span>
        </button>
    </nav>

    <!-- MATCH CELEBRATION OVERLAY POPUP -->
    <div id="matchOverlay" class="hidden fixed inset-0 bg-black/90 z-50 flex flex-col items-center justify-center p-6 text-center space-y-6">
        <div class="relative flex items-center justify-center gap-4">
            <img id="matchUserAvatar" src="https://images.unsplash.com/photo-1534528741775-53994a69daeb?auto=format&fit=crop&q=80&w=150" class="w-24 h-24 rounded-full object-cover border-4 border-pink-500 shadow-2xl">
            <div class="w-12 h-12 bg-pink-500 rounded-full flex items-center justify-center text-white text-xl animate-pulse shadow-lg glow-pink">
                <i class="fa-solid fa-heart"></i>
            </div>
            <img id="matchTargetAvatar" src="" class="w-24 h-24 rounded-full object-cover border-4 border-telegram-accent shadow-2xl">
        </div>

        <div>
            <h2 class="text-3xl font-extrabold text-transparent bg-clip-text bg-gradient-to-r from-pink-500 to-purple-400">IT'S A MATCH!</h2>
            <p class="text-sm text-gray-300 mt-2">You and <span id="matchTargetName" class="font-bold text-white">Sarah</span> liked each other!</p>
        </div>

        <div class="w-full space-y-3 max-w-xs">
            <button onclick="openDirectMatchChat()" class="w-full py-3 bg-gradient-to-r from-pink-500 to-rose-600 text-white font-bold text-sm rounded-xl shadow-lg glow-pink">
                Send Direct Message
            </button>
            <button onclick="closeMatchOverlay()" class="w-full py-3 bg-[#242f3d] text-gray-300 font-bold text-sm rounded-xl border border-gray-700">
                Keep Swiping
            </button>
        </div>
    </div>

    <!-- TELEGRAM STARS PAYMENT SIMULATION MODAL -->
    <div id="starsInvoiceModal" class="hidden fixed inset-0 bg-black/80 z-50 flex items-center justify-center p-4">
        <div class="bg-[#17212b] border border-yellow-500/30 rounded-2xl p-5 w-full max-w-xs text-center space-y-4">
            <div class="w-12 h-12 rounded-full bg-yellow-400/20 text-yellow-400 flex items-center justify-center mx-auto text-2xl">
                <i class="fa-solid fa-star"></i>
            </div>
            <div>
                <h3 class="text-lg font-bold text-white">Confirm Payment</h3>
                <p class="text-xs text-gray-400 mt-1">Item: <span id="modalProductName" class="text-white font-semibold">VIP Pass</span></p>
            </div>
            <div class="bg-[#242f3d] p-3 rounded-xl flex justify-between items-center">
                <span class="text-xs text-gray-300">Total Price:</span>
                <span class="text-sm font-bold text-yellow-400 flex items-center gap-1">
                    <i class="fa-solid fa-star text-xs"></i> <span id="modalProductPrice">150</span> Stars
                </span>
            </div>
            <p class="text-[10px] text-gray-400">Payment will be deducted from your Telegram Account Stars balance.</p>
            <div class="flex gap-2">
                <button onclick="closePaymentModal()" class="flex-1 py-2 bg-[#242f3d] text-gray-300 text-xs font-bold rounded-xl">Cancel</button>
                <button onclick="confirmTelegramStarsPayment()" class="flex-1 py-2 bg-yellow-400 text-black text-xs font-extrabold rounded-xl shadow-md glow-star">Pay Now</button>
            </div>
        </div>
    </div>

    <!-- NOTIFICATION TOAST TOAST CONTAINER -->
    <div id="toast" class="hidden fixed top-14 left-1/2 -translate-x-1/2 bg-telegram-accent text-white text-xs px-4 py-2 rounded-full shadow-lg z-50 flex items-center gap-2">
        <i class="fa-solid fa-circle-info"></i> <span id="toastMsg">Action completed!</span>
    </div>

    <script>
        // MOCK PROFILES DATABASE FOR SWIPE DECK
        const MOCK_PROFILES = [
            {
                id: 101,
                name: "Sarah",
                age: 24,
                distance: "3 km away",
                bio: "UX Designer in Dubai ✨ Coffee lover, weekend hiker, and synthwave enthusiast.",
                tags: ["🎨 Design", "☕ Coffee", "⛰️ Hiking"],
                verified: true,
                avatar: "https://images.unsplash.com/photo-1494790108377-be9c29b29330?auto=format&fit=crop&q=80&w=600"
            },
            {
                id: 102,
                name: "Elena",
                age: 25,
                distance: "7 km away",
                bio: "Crypto trader & Telegram bot enthusiast. Let's grab sushi and talk Web3 🍣",
                tags: ["🚀 Crypto", "🍣 Sushi", "🐱 Cats"],
                verified: true,
                avatar: "https://images.unsplash.com/photo-1517841905240-472988babdf9?auto=format&fit=crop&q=80&w=600"
            },
            {
                id: 103,
                name: "Jessica",
                age: 23,
                distance: "12 km away",
                bio: "Photographer & travel addict. Always searching for the best sunset spots 🌅",
                tags: ["📸 Photo", "✈️ Travel", "🎶 Techno"],
                verified: false,
                avatar: "https://images.unsplash.com/photo-1524504388940-b1c1722653e1?auto=format&fit=crop&q=80&w=600"
            },
            {
                id: 104,
                name: "Maya",
                age: 26,
                distance: "1 km away",
                bio: "Fitness trainer & foodie. Teach me how to code and I'll build your workout plan! 💪",
                tags: ["🏋️ Fitness", "🍕 Foodie", "📚 Books"],
                verified: true,
                avatar: "https://images.unsplash.com/photo-1539571696357-5a69c17a67c6?auto=format&fit=crop&q=80&w=600"
            }
        ];

        // APP STATE
        let currentUser = {
            name: "Faiz",
            age: 26,
            bio: "Full-stack telegram dev building the future of social apps. 🚀 Coffee lover.",
            lookingFor: "Women",
            stars: 250,
            isVip: false,
            likesCount: 128,
            matchesCount: 14,
            avatar: "https://images.unsplash.com/photo-1534528741775-53994a69daeb?auto=format&fit=crop&q=80&w=300"
        };

        let cardQueue = [...MOCK_PROFILES];
        let currentCardIndex = 0;
        let lastSwipedCard = null;
        let matchesList = [
            { id: 101, name: "Sarah", avatar: "https://images.unsplash.com/photo-1494790108377-be9c29b29330?auto=format&fit=crop&q=80&w=150", lastMsg: "Hey! Loved your profile bio 😄" }
        ];
        let chatHistories = {
            101: [
                { sender: "Sarah", text: "Hey! Loved your profile bio 😄", time: "10:14 AM" }
            ]
        };
        let activeChatTarget = null;
        let pendingPurchase = null;

        // INITIALIZATION
        window.onload = function() {
            lucide.createIcons();
            renderCardDeck();
            renderMatchesHorizontal();
            renderChatsList();
            updateUserUI();
        };

        // USER UI REFRESH
        function updateUserUI() {
            document.getElementById('starsBalance').innerText = currentUser.stars;
            document.getElementById('userHeaderName').innerText = currentUser.name;
            document.getElementById('userProfileNameDisplay').innerText = currentUser.name;
            document.getElementById('userAgeDisplay').innerText = currentUser.age;
            document.getElementById('userBioDisplay').innerText = `"${currentUser.bio}"`;
            document.getElementById('statLikesCount').innerText = currentUser.likesCount;
            document.getElementById('statMatchesCount').innerText = currentUser.matchesCount;

            if(currentUser.isVip) {
                document.getElementById('userVipBadge').classList.remove('hidden');
            }
        }

        // NAVIGATION TABS
        function switchTab(tabName) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
            document.querySelectorAll('.nav-btn').forEach(el => {
                el.classList.remove('text-telegram-accent');
                el.classList.add('text-gray-400');
            });

            document.getElementById(`tab-${tabName}`).classList.remove('hidden');
            const activeNav = document.getElementById(`nav-${tabName}`);
            activeNav.classList.remove('text-gray-400');
            activeNav.classList.add('text-telegram-accent');

            if (tabName === 'chat') {
                document.getElementById('chatUnreadDot').classList.add('hidden');
            }
        }

        // RENDER SWIPE CARD STACK
        function renderCardDeck() {
            const container = document.getElementById('cardDeck');
            container.innerHTML = '';

            if (currentCardIndex >= cardQueue.length) {
                container.innerHTML = `
                    <div class="text-center p-6 space-y-3">
                        <div class="w-16 h-16 bg-[#242f3d] text-telegram-accent rounded-full flex items-center justify-center mx-auto text-2xl pulse-animation">
                            <i class="fa-solid fa-radar"></i>
                        </div>
                        <h3 class="text-lg font-bold text-white">No More Profiles Nearby</h3>
                        <p class="text-xs text-gray-400">Expand your distance filter or boost your profile to get more matches!</p>
                        <button onclick="resetCards()" class="px-4 py-2 bg-telegram-accent text-white font-bold text-xs rounded-xl shadow-md">
                            Reset Stack
                        </button>
                    </div>
                `;
                return;
            }

            // Render top two cards for depth
            for (let i = Math.min(currentCardIndex + 1, cardQueue.length - 1); i >= currentCardIndex; i--) {
                const profile = cardQueue[i];
                const isTop = i === currentCardIndex;

                const cardEl = document.createElement('div');
                cardEl.className = `swipe-card absolute inset-0 w-full h-full rounded-2xl overflow-hidden bg-[#242f3d] border border-gray-800 shadow-2xl flex flex-col justify-end ${isTop ? 'z-20' : 'z-10 scale-95 opacity-80'}`;
                
                cardEl.innerHTML = `
                    <img src="${profile.avatar}" class="absolute inset-0 w-full h-full object-cover">
                    <div class="absolute inset-0 bg-gradient-to-t from-black/90 via-black/30 to-transparent"></div>

                    <!-- Swipe Status Badges -->
                    <div id="badge-like-${profile.id}" class="badge-like hidden absolute top-6 left-6 text-xl font-black px-3 py-1 rounded-md z-30">LIKE</div>
                    <div id="badge-nope-${profile.id}" class="badge-nope hidden absolute top-6 right-6 text-xl font-black px-3 py-1 rounded-md z-30">NOPE</div>
                    <div id="badge-super-${profile.id}" class="badge-super hidden absolute top-12 left-1/2 -translate-x-1/2 text-xl font-black px-3 py-1 rounded-md z-30">SUPER LIKE</div>

                    <!-- Profile Info Overlay -->
                    <div class="relative p-4 z-20 space-y-2">
                        <div class="flex items-center gap-2">
                            <h2 class="text-2xl font-black text-white">${profile.name}, ${profile.age}</h2>
                            ${profile.verified ? '<i class="fa-solid fa-circle-check text-telegram-accent text-base"></i>' : ''}
                        </div>
                        <p class="text-xs text-gray-300 flex items-center gap-1">
                            <i class="fa-solid fa-location-dot text-rose-500"></i> ${profile.distance}
                        </p>
                        <p class="text-xs text-gray-200 line-clamp-2">${profile.bio}</p>
                        <div class="flex flex-wrap gap-1.5 pt-1">
                            ${profile.tags.map(tag => `<span class="text-[10px] bg-black/50 backdrop-blur-md text-white px-2 py-0.5 rounded-full border border-white/20">${tag}</span>`).join('')}
                        </div>
                    </div>
                `;

                if (isTop) {
                    attachTouchEvents(cardEl, profile);
                }

                container.appendChild(cardEl);
            }
        }

        // TOUCH & MOUSE SWIPE LOGIC
        function attachTouchEvents(card, profile) {
            let startX = 0, startY = 0, currentX = 0, currentY = 0;
            let isDragging = false;

            const onStart = (e) => {
                isDragging = true;
                card.classList.add('swiping');
                startX = e.type.includes('touch') ? e.touches[0].clientX : e.clientX;
                startY = e.type.includes('touch') ? e.touches[0].clientY : e.clientY;
            };

            const onMove = (e) => {
                if (!isDragging) return;
                currentX = (e.type.includes('touch') ? e.touches[0].clientX : e.clientX) - startX;
                currentY = (e.type.includes('touch') ? e.touches[0].clientY : e.clientY) - startY;

                const rotate = currentX * 0.08;
                card.style.transform = `translate3d(${currentX}px, ${currentY}px, 0) rotate(${rotate}deg)`;

                // Toggle dynamic indicators
                const likeBadge = document.getElementById(`badge-like-${profile.id}`);
                const nopeBadge = document.getElementById(`badge-nope-${profile.id}`);
                const superBadge = document.getElementById(`badge-super-${profile.id}`);

                if (currentY < -80 && Math.abs(currentX) < 50) {
                    superBadge?.classList.remove('hidden');
                    likeBadge?.classList.add('hidden');
                    nopeBadge?.classList.add('hidden');
                } else if (currentX > 50) {
                    likeBadge?.classList.remove('hidden');
                    nopeBadge?.classList.add('hidden');
                    superBadge?.classList.add('hidden');
                } else if (currentX < -50) {
                    nopeBadge?.classList.remove('hidden');
                    likeBadge?.classList.add('hidden');
                    superBadge?.classList.add('hidden');
                } else {
                    likeBadge?.classList.add('hidden');
                    nopeBadge?.classList.add('hidden');
                    superBadge?.classList.add('hidden');
                }
            };

            const onEnd = () => {
                if (!isDragging) return;
                isDragging = false;
                card.classList.remove('swiping');

                if (currentX > 100) {
                    completeSwipe('right', card, profile);
                } else if (currentX < -100) {
                    completeSwipe('left', card, profile);
                } else if (currentY < -100) {
                    completeSwipe('up', card, profile);
                } else {
                    card.style.transform = 'translate3d(0,0,0) rotate(0deg)';
                    document.getElementById(`badge-like-${profile.id}`)?.classList.add('hidden');
                    document.getElementById(`badge-nope-${profile.id}`)?.classList.add('hidden');
                    document.getElementById(`badge-super-${profile.id}`)?.classList.add('hidden');
                }
            };

            card.addEventListener('mousedown', onStart);
            window.addEventListener('mousemove', onMove);
            window.addEventListener('mouseup', onEnd);

            card.addEventListener('touchstart', onStart);
            window.addEventListener('touchmove', onMove);
            window.addEventListener('touchend', onEnd);
        }

        // TRIGGER BUTTON SWIPE
        function handleSwipe(direction) {
            const currentProfile = cardQueue[currentCardIndex];
            if (!currentProfile) return;
            const topCard = document.querySelector('#cardDeck .swipe-card.z-20');
            completeSwipe(direction, topCard, currentProfile);
        }

        function completeSwipe(direction, card, profile) {
            lastSwipedCard = profile;

            if (card) {
                let offscreenX = 0, offscreenY = 0;
                if (direction === 'right') offscreenX = 500;
                if (direction === 'left') offscreenX = -500;
                if (direction === 'up') offscreenY = -500;

                card.style.transition = 'transform 0.4s ease, opacity 0.4s ease';
                card.style.transform = `translate3d(${offscreenX}px, ${offscreenY}px, 0) rotate(${offscreenX * 0.05}deg)`;
                card.style.opacity = '0';
            }

            setTimeout(() => {
                currentCardIndex++;
                renderCardDeck();

                // Trigger Match on Like or Super Like for demo
                if (direction === 'right' || direction === 'up') {
                    triggerMatch(profile);
                }
            }, 300);
        }

        function rewindSwipe() {
            if (currentCardIndex > 0) {
                currentCardIndex--;
                renderCardDeck();
                showToast("Rewound to previous profile!");
            } else {
                showToast("No swipes to rewind!");
            }
        }

        function resetCards() {
            currentCardIndex = 0;
            renderCardDeck();
        }

        // MATCH TRIGGER OVERLAY
        function triggerMatch(targetProfile) {
            document.getElementById('matchTargetName').innerText = targetProfile.name;
            document.getElementById('matchUserAvatar').src = currentUser.avatar;
            document.getElementById('matchTargetAvatar').src = targetProfile.avatar;
            document.getElementById('matchOverlay').classList.remove('hidden');

            // Add to matches if not existing
            if (!matchesList.some(m => m.id === targetProfile.id)) {
                matchesList.unshift({
                    id: targetProfile.id,
                    name: targetProfile.name,
                    avatar: targetProfile.avatar,
                    lastMsg: "Matched just now!"
                });
                chatHistories[targetProfile.id] = [
                    { sender: targetProfile.name, text: `Hey Faiz! Glad we matched! ✨`, time: "Just now" }
                ];
                currentUser.matchesCount++;
                updateUserUI();
                renderMatchesHorizontal();
                renderChatsList();
            }
        }

        function closeMatchOverlay() {
            document.getElementById('matchOverlay').classList.add('hidden');
        }

        function openDirectMatchChat() {
            closeMatchOverlay();
            const newestMatch = matchesList[0];
            if (newestMatch) {
                openChatScreen(newestMatch.id);
                switchTab('chat');
            }
        }

        // CHAT RENDERERS & LOGIC
        function renderMatchesHorizontal() {
            const container = document.getElementById('matchesHorizontalList');
            container.innerHTML = matchesList.map(m => `
                <div onclick="openChatScreen(${m.id})" class="flex flex-col items-center gap-1 cursor-pointer shrink-0">
                    <div class="relative">
                        <img src="${m.avatar}" class="w-12 h-12 rounded-full object-cover border-2 border-pink-500">
                        <span class="absolute bottom-0 right-0 w-3 h-3 bg-green-500 rounded-full border-2 border-[#17212b]"></span>
                    </div>
                    <span class="text-[10px] font-medium text-gray-300">${m.name}</span>
                </div>
            `).join('');
        }

        function renderChatsList() {
            const container = document.getElementById('chatsList');
            container.innerHTML = matchesList.map(m => `
                <div onclick="openChatScreen(${m.id})" class="p-3 flex items-center gap-3 hover:bg-[#1f2a38] cursor-pointer transition">
                    <img src="${m.avatar}" class="w-11 h-11 rounded-full object-cover">
                    <div class="flex-1 overflow-hidden">
                        <div class="flex justify-between items-center mb-0.5">
                            <h4 class="text-xs font-bold text-white">${m.name}</h4>
                            <span class="text-[10px] text-gray-500">12:30 PM</span>
                        </div>
                        <p class="text-xs text-gray-400 truncate">${m.lastMsg}</p>
                    </div>
                </div>
            `).join('');
        }

        function openChatScreen(matchId) {
            activeChatTarget = matchesList.find(m => m.id === matchId);
            if (!activeChatTarget) return;

            document.getElementById('chatHeaderName').innerText = activeChatTarget.name;
            document.getElementById('chatHeaderAvatar').src = activeChatTarget.avatar;
            document.getElementById('activeChatScreen').classList.remove('hidden');

            renderMessages();
        }

        function closeChatScreen() {
            document.getElementById('activeChatScreen').classList.add('hidden');
        }

        function renderMessages() {
            if (!activeChatTarget) return;
            const msgs = chatHistories[activeChatTarget.id] || [];
            const container = document.getElementById('chatMessages');

            container.innerHTML = msgs.map(msg => {
                const isMe = msg.sender === 'me';
                return `
                    <div class="flex flex-col ${isMe ? 'items-end' : 'items-start'}">
                        <div class="max-w-[75%] px-3.5 py-2 rounded-2xl text-xs ${isMe ? 'bg-telegram-accent text-white rounded-br-none' : 'bg-[#242f3d] text-gray-200 rounded-bl-none'}">
                            ${msg.text}
                        </div>
                        <span class="text-[9px] text-gray-500 mt-1 px-1">${msg.time}</span>
                    </div>
                `;
            }).join('');

            container.scrollTop = container.scrollHeight;
        }

        function sendChatMessage() {
            const input = document.getElementById('chatInput');
            const text = input.value.trim();
            if (!text || !activeChatTarget) return;

            chatHistories[activeChatTarget.id].push({
                sender: 'me',
                text: text,
                time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
            });

            input.value = '';
            renderMessages();

            // Dynamic Bot Cute Automated Reply
            setTimeout(() => {
                const cuteReplies = [
                    "Aww, that's so sweet! 🥰",
                    "Haha I totaly agree! What else do you like to do?",
                    "That sounds amazing! We should definitely hang out soon ✨",
                    "Tell me more about yourself! ☕"
                ];
                const randomReply = cuteReplies[Math.floor(Math.random() * cuteReplies.length)];

                chatHistories[activeChatTarget.id].push({
                    sender: activeChatTarget.name,
                    text: randomReply,
                    time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
                });
                renderMessages();
            }, 1200);
        }

        function sendQuickMessage(text) {
            document.getElementById('chatInput').value = text;
            sendChatMessage();
        }

        function triggerTelegramCall() {
            showToast("Calling inside Telegram WebApp...");
        }

        // TELEGRAM STARS SHOP & MONETIZATION
        function purchaseProduct(name, price) {
            pendingPurchase = { name, price };
            document.getElementById('modalProductName').innerText = name;
            document.getElementById('modalProductPrice').innerText = price;
            document.getElementById('starsInvoiceModal').classList.remove('hidden');
        }

        function closePaymentModal() {
            document.getElementById('starsInvoiceModal').classList.add('hidden');
        }

        function confirmTelegramStarsPayment() {
            if (!pendingPurchase) return;

            if (currentUser.stars >= pendingPurchase.price) {
                currentUser.stars -= pendingPurchase.price;
                if (pendingPurchase.name.includes('VIP')) {
                    currentUser.isVip = true;
                }
                updateUserUI();
                closePaymentModal();
                showToast(`Successfully purchased ${pendingPurchase.name}!`);
            } else {
                showToast("Insufficient Stars balance!");
            }
        }

        function watchRewardedAd() {
            showToast("Loading Video Ad... (+5 Daily Swipes)");
            setTimeout(() => {
                showToast("Ad finished! +5 Extra Swipes added.");
            }, 2000);
        }

        function openBoostModal() {
            purchaseProduct('30 Min Spotlight Boost', 75);
        }

        // PROFILE EDIT & HELPERS
        function saveProfileData() {
            currentUser.bio = document.getElementById('inputBio').value;
            currentUser.age = document.getElementById('inputAge').value;
            currentUser.lookingFor = document.getElementById('inputLookingFor').value;
            updateUserUI();
            showToast("Profile details updated successfully!");
        }

        function triggerPhotoChange() {
            showToast("Select photo from Telegram library...");
        }

        function copyReferralLink() {
            const dummy = document.createElement('input');
            document.body.appendChild(dummy);
            dummy.value = 'https://t.me/SparkMatchBot?start=ref_faiz';
            dummy.select();
            document.execCommand('copy');
            document.body.removeChild(dummy);
            showToast("Referral link copied to clipboard!");
        }

        function showToast(msg) {
            const toast = document.getElementById('toast');
            document.getElementById('toastMsg').innerText = msg;
            toast.classList.remove('hidden');
            setTimeout(() => {
                toast.classList.add('hidden');
            }, 2500);
        }
    </script>
</body>
</html>
```
