```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Community Events - Profile Photo Upload</title>
    <style>
        /* --- UX Design Token System --- */
        :root {
            --primary: #2563eb;       
            --primary-hover: #1d4ed8;
            --surface-bg: #f8fafc;   
            --surface-card: #ffffff; 
            --text-main: #0f172a;    
            --text-muted: #64748b;   
            --border-subtle: #e2e8f0;
            --accent-green: #dcfce7;
            --accent-green-text: #15803d;
            --radius-md: 12px;
            --radius-lg: 16px;
            --transition-smooth: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            -webkit-font-smoothing: antialiased;
        }

        body {
            background-color: #0f172a; 
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
            gap: 20px;
        }

        /* --- UX Flow Quick-Navigation Bar --- */
        .flow-controller {
            background: rgba(30, 41, 59, 0.8);
            backdrop-filter: blur(8px);
            border: 1px solid #334155;
            padding: 10px;
            border-radius: var(--radius-md);
            display: flex;
            gap: 8px;
            max-width: 375px;
            width: 100%;
            justify-content: center;
            flex-wrap: wrap;
            z-index: 100;
        }

        .flow-btn {
            background: #334155;
            color: #f8fafc;
            border: none;
            padding: 6px 12px;
            border-radius: 6px;
            font-size: 0.75rem;
            font-weight: 600;
            cursor: pointer;
            transition: var(--transition-smooth);
        }

        .flow-btn:hover {
            background: var(--primary);
        }

        .flow-btn.active-flow {
            background: var(--primary);
            box-shadow: 0 0 8px rgba(37, 99, 235, 0.5);
        }

        /* --- Device Frame Wrapper --- */
        .viewport-container {
            width: 375px; 
            height: 720px;
            background: var(--surface-bg);
            border: 10px solid #1e293b;
            border-radius: 40px;
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.5);
            position: relative;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        /* --- Header Component --- */
        header {
            background-color: var(--surface-card);
            border-bottom: 1px solid var(--border-subtle);
            padding: 20px 24px 16px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            min-height: 64px;
            z-index: 10;
        }

        header h1 {
            font-size: 1.25rem;
            font-weight: 700;
            color: var(--text-main);
            letter-spacing: -0.02em;
        }

        /* --- Main Content Area --- */
        .main-scroll-view {
            flex: 1;
            overflow-y: auto;
            padding: 24px;
            padding-bottom: 90px; 
            scroll-behavior: smooth;
        }

        /* --- Navigation Component --- */
        nav {
            position: absolute;
            bottom: 0;
            width: 100%;
            background: rgba(255, 255, 255, 0.92);
            backdrop-filter: blur(12px);
            border-top: 1px solid var(--border-subtle);
            display: flex;
            justify-content: space-around;
            padding: 12px 0 max(12px, env(safe-area-inset-bottom));
            z-index: 10;
            transition: var(--transition-smooth);
        }

        .nav-item {
            background: none;
            border: none;
            color: var(--text-muted);
            font-size: 0.7rem;
            font-weight: 500;
            cursor: pointer;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 4px;
            transition: var(--transition-smooth);
            width: 55px;
        }

        .nav-item svg {
            width: 20px;
            height: 20px;
            stroke: currentColor;
            fill: none;
            stroke-width: 2;
        }

        .nav-item.active { color: var(--primary); }
        .nav-item.active svg { stroke: var(--primary); }

        /* --- Global Architectural Controls --- */
        .nav-locked { display: none !important; }

        .screen { display: none; animation: fadeIn 0.3s ease-out; }
        .screen.active { display: block !important; }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(8px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* --- UI Elements --- */
        .section-title {
            font-size: 0.875rem;
            text-transform: uppercase;
            letter-spacing: 0.05em;
            color: var(--text-muted);
            margin-bottom: 16px;
            font-weight: 600;
        }

        .card {
            background: var(--surface-card);
            border: 1px solid var(--border-subtle);
            border-radius: var(--radius-md);
            padding: 16px;
            margin-bottom: 16px;
            cursor: pointer;
            position: relative;
            transition: var(--transition-smooth);
        }

        .card:hover {
            border-color: #cbd5e1;
            transform: translateY(-2px);
        }

        .card h3 {
            font-size: 1.05rem;
            color: var(--text-main);
            font-weight: 600;
            margin-bottom: 6px;
            padding-right: 32px; 
        }

        .card p {
            font-size: 0.875rem;
            color: var(--text-muted);
            line-height: 1.4;
            margin-bottom: 12px;
        }

        .card-meta {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .card-edit-trigger {
            position: absolute;
            top: 14px;
            right: 14px;
            padding: 6px;
            background: #f1f5f9;
            color: var(--text-muted);
            border: none;
            border-radius: 50%;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: var(--transition-smooth);
        }

        .card-edit-trigger:hover {
            background: var(--primary);
            color: white;
        }

        .badge {
            display: inline-flex;
            padding: 4px 8px;
            border-radius: 6px;
            font-size: 0.75rem;
            font-weight: 600;
            background: #f1f5f9;
            color: #475569;
        }

        .badge.joined {
            background: var(--accent-green);
            color: var(--accent-green-text);
        }

        /* --- Members Directory List Styling --- */
        .member-row {
            display: flex;
            align-items: center;
            gap: 12px;
            background: var(--surface-card);
            border: 1px solid var(--border-subtle);
            padding: 12px 16px;
            border-radius: var(--radius-md);
            margin-bottom: 10px;
        }

        .member-avatar-container {
            width: 44px;
            height: 44px;
            border-radius: 50%;
            overflow: hidden;
            background: #e2e8f0;
            display: flex;
            align-items: center;
            justify-content: center;
            flex-shrink: 0;
            color: var(--text-muted);
        }

        .member-avatar-container img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }

        .member-info {
            flex: 1;
            min-width: 0;
        }

        .member-info h4 {
            font-size: 0.95rem;
            color: var(--text-main);
            font-weight: 600;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        .member-info p {
            font-size: 0.8rem;
            color: var(--text-muted);
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        /* --- Empty State Box --- */
        .empty-state {
            text-align: center;
            padding: 40px 20px;
            background: var(--surface-card);
            border: 1px dashed var(--border-subtle);
            border-radius: var(--radius-md);
            color: var(--text-muted);
            font-size: 0.9rem;
        }

        .empty-state svg {
            color: #cbd5e1;
            margin-bottom: 12px;
        }

        /* --- Photo Picker Layout Slots --- */
        .avatar-uploader {
            text-align: center;
            margin-bottom: 24px;
            position: relative;
        }

        .avatar-preview-frame {
            width: 100px;
            height: 100px;
            border-radius: 50%;
            background-color: #e2e8f0;
            border: 2px dashed #cbd5e1;
            margin: 0 auto 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            overflow: hidden;
            position: relative;
            cursor: pointer;
            transition: var(--transition-smooth);
        }

        .avatar-preview-frame:hover {
            border-color: var(--primary);
            background-color: #edf2f7;
        }

        .avatar-image-render {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: none;
        }

        .upload-hint-icon {
            color: var(--text-muted);
            transition: var(--transition-smooth);
        }
        
        .avatar-preview-frame:hover .upload-hint-icon {
            color: var(--primary);
            transform: scale(1.1);
        }

        /* --- Inputs & Buttons --- */
        .btn-primary {
            background-color: var(--primary);
            color: white;
            border: none;
            padding: 14px 20px;
            border-radius: var(--radius-md);
            width: 100%;
            cursor: pointer;
            font-size: 0.95rem;
            font-weight: 600;
            transition: var(--transition-smooth);
            display: inline-flex;
            justify-content: center;
            align-items: center;
            gap: 8px;
        }

        .btn-primary:hover { background-color: var(--primary-hover); }

        .btn-secondary {
            background-color: transparent;
            color: var(--text-muted);
            border: 1px solid var(--border-subtle);
            padding: 12px 20px;
            border-radius: var(--radius-md);
            width: 100%;
            cursor: pointer;
            font-size: 0.95rem;
            font-weight: 600;
            text-align: center;
            transition: var(--transition-smooth);
        }
        .btn-secondary:hover {
            background: #f1f5f9;
            color: var(--text-main);
        }

        .form-group { margin-bottom: 18px; }
        .form-group label { display: block; font-size: 0.85rem; font-weight: 600; margin-bottom: 6px; color: var(--text-main); }
        .form-group input, .form-group textarea { width: 100%; padding: 12px; border: 1px solid var(--border-subtle); border-radius: var(--radius-md); font-size: 0.95rem; color: var(--text-main); font-family: inherit;}
        .form-group input:focus, .form-group textarea:focus { outline: 2px solid var(--primary); border-color: transparent; }

        /* --- Toast Banner Notification --- */
        .toast-banner {
            position: absolute;
            top: 64px;
            left: 0;
            right: 0;
            background-color: #0f172a;
            color: #fff;
            padding: 10px 16px;
            font-size: 0.8rem;
            text-align: center;
            border-bottom: 1px solid #334155;
            display: none;
            z-index: 20;
            animation: slideDown 0.25s ease-out;
        }

        @keyframes slideDown {
            from { transform: translateY(-100%); }
            to { transform: translateY(0); }
        }

        /* --- Custom Interactive Filter Tabs --- */
        .filter-tabs {
            display: flex;
            gap: 8px;
            margin-bottom: 20px;
        }
        .filter-tab {
            background: #e2e8f0;
            color: var(--text-muted);
            padding: 6px 14px;
            border-radius: 20px;
            font-size: 0.8rem;
            font-weight: 600;
            border: none;
            cursor: pointer;
            transition: var(--transition-smooth);
        }
        .filter-tab.active {
            background: var(--text-main);
            color: white;
        }

        .hidden-file-input { display: none; }
    </style>
</head>
<body>

    <!-- UX Quick Flow Toolbar -->
    <div class="flow-controller">
        <button id="flow-gate" class="flow-btn active-flow" onclick="devBypassRoute('gate')">1. Signup Gate</button>
        <button id="flow-dashboard" class="flow-btn" onclick="devBypassRoute('dashboard')">2. Dashboard</button>
        <button id="flow-members" class="flow-btn" onclick="devBypassRoute('members')">3. Members Directory</button>
        <button id="flow-form" class="flow-btn" onclick="devBypassRoute('form')">4. Event Form</button>
        <button id="flow-profile" class="flow-btn" onclick="devBypassRoute('profile')">5. Profile</button>
    </div>

    <!-- Main Viewport Context Frame -->
    <div class="viewport-container">
        
        <header>
            <h1 id="view-title">Join Community</h1>
            <div id="header-action-slot"></div>
        </header>

        <div id="toast" class="toast-banner">Changes updated successfully!</div>

        <div class="main-scroll-view" id="scroll-container">

            <!-- SCREEN 1: SIGNUP GATE -->
            <div id="screen-gate" class="screen active">
                <p style="color: var(--text-muted); font-size: 0.9rem; margin-bottom: 24px;">Create your account to browse, track, and manage localized community events near you.</p>
                
                <form id="signup-form" onsubmit="handleSignup(event)">
                    <div class="form-group">
                        <label for="reg-name">Full Name</label>
                        <input type="text" id="reg-name" placeholder="John Doe" required>
                    </div>
                    <div class="form-group">
                        <label for="reg-email">Email Address</label>
                        <input type="email" id="reg-email" placeholder="john@example.com" required>
                    </div>
                    <div class="form-group" style="margin-bottom: 28px;">
                        <label for="reg-password">Password</label>
                        <input type="password" id="reg-password" placeholder="••••••••" required>
                    </div>
                    <button type="submit" class="btn-primary">
                        Get Started
                        <svg width="18" height="18" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M13.5 4.5L21 12m0 0l-7.5 7.5M21 12H3"/></svg>
                    </button>
                </form>
            </div>

            <!-- SCREEN 2: DASHBOARD -->
            <div id="screen-dashboard" class="screen">
                <div class="filter-tabs">
                    <button class="filter-tab active" id="filter-all" onclick="setFilter('all')">All Events</button>
                    <button class="filter-tab" id="filter-joined" onclick="setFilter('joined')">My Schedule</button>
                </div>
                
                <div class="section-title" id="list-context-title">Upcoming events</div>
                <div id="events-list-container"></div>
            </div>

            <!-- SCREEN 3: MEMBERS DIRECTORY -->
            <div id="screen-members" class="screen">
                <p style="color: var(--text-muted); font-size: 0.85rem; margin-bottom: 20px;">Connect with your neighbors and fellow municipal event organizers.</p>
                <div class="section-title" id="members-count-title">Community Registry</div>
                <div id="members-list-container"></div>
            </div>

            <!-- SCREEN 4: ADD/EDIT EVENT FORM -->
            <div id="screen-form" class="screen">
                <form id="event-mutation-form" onsubmit="handleEventSubmit(event)">
                    <input type="hidden" id="edit-event-id" value="">
                    
                    <div class="form-group">
                        <label for="evt-title">Event Name</label>
                        <input type="text" id="evt-title" placeholder="e.g. Clean Up Day" required>
                    </div>
                    <div class="form-group">
                        <label for="evt-date">Date & Time</label>
                        <input type="datetime-local" id="evt-date" required>
                    </div>
                    <div class="form-group">
                        <label for="evt-desc">Description</label>
                        <textarea id="evt-desc" rows="4" placeholder="Describe what's happening..." required></textarea>
                    </div>
                    
                    <div style="display: flex; flex-direction: column; gap: 10px; margin-top: 24px;">
                        <button type="submit" class="btn-primary" id="form-submit-btn">Publish Event</button>
                        <button type="button" class="btn-secondary" onclick="navigateTo('dashboard')">Cancel</button>
                    </div>
                </form>
            </div>

            <!-- SCREEN 5: PROFILE MANAGEMENT WITH PHOTO UPLOAD -->
            <div id="screen-profile" class="screen">
                <div class="avatar-uploader">
                    <!-- Interactive Upload Circle Frame Template -->
                    <div class="avatar-preview-frame" onclick="triggerAvatarSelection()">
                        <svg class="upload-hint-icon" id="avatar-hint-svg" width="32" height="32" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M6.827 6.175A2.31 2.31 0 0110 5.657a.502.502 0 00.326-.115l.922-.738c.524-.42 1.176-.648 1.85-.648h1.804c.674 0 1.326.228 1.85.648l.922.738a.502.502 0 00.325.115 2.31 2.31 0 013.173 2.197V16.5a2.25 2.25 0 01-2.25 2.25H6.75A2.25 2.25 0 014.5 16.5v-8.15c0-1.047.702-1.954 1.727-2.175z"/><path stroke-linecap="round" stroke-linejoin="round" d="M15 11.25a3 3 0 11-6 0 3 3 0 016 0z"/></svg>
                        <img id="avatar-img" class="avatar-image-render" alt="Live Avatar Mirror Matrix">
                    </div>
                    <!-- Hidden File Input Layer Bridge -->
                    <input type="file" id="hidden-avatar-input" class="hidden-file-input" accept="image/*" onchange="processAvatarUpload(event)">
                    <p style="font-size: 0.8rem; color: var(--text-muted); font-weight: 500;">Tap circle to upload profile picture</p>
                </div>

                <form id="profile-update-form" onsubmit="handleProfileUpdate(event)">
                    <div class="form-group">
                        <label for="prof-name">Display Name</label>
                        <input type="text" id="prof-name" required>
                    </div>
                    <div class="form-group" style="margin-bottom: 28px;">
                        <label for="prof-email">Email Address</label>
                        <input type="email" id="prof-email" required>
                    </div>
                    <button type="submit" class="btn-primary">Save and Update Profile</button>
                </form>
            </div>

        </div>

        <!-- Global Navigation Context Bar -->
        <nav id="global-nav" class="nav-locked">
            <button class="nav-item active" id="nav-dashboard" onclick="navigateTo('dashboard')">
                <svg viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M2.25 12l8.954-8.955c.44-.439 1.152-.439 1.591 0L21.75 12M4.5 9.75v10.125c0 .621.504 1.125 1.125 1.125H9.75v-4.875c0-.621.504-1.125 1.125-1.125h2.25c.621 0 1.125.504 1.125 1.125V21h4.125c.621 0 1.125-.504 1.125-1.125V9.75M8.25 21h8.25"/></svg>
                Events
            </button>
            <button class="nav-item" id="nav-members" onclick="navigateTo('members')">
                <svg viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15 19.128a9.38 9.38 0 002.625.372 9.337 9.337 0 004.121-.952 4.125 4.125 0 00-7.533-2.493M15 19.128v-.003c0-1.113-.285-2.16-.786-3.07M15 19.128v.106A12.318 12.318 0 018.624 21c-2.331 0-4.512-.645-6.374-1.766l-.001-.109a6.375 6.375 0 0111.964-3.07M12 6.375a3.375 3.375 0 11-6.75 0 3.375 3.375 0 016.75 0zm8.25 2.25a2.625 2.625 0 11-5.25 0 2.625 2.625 0 015.25 0z"/></svg>
                Members
            </button>
            <button class="nav-item" id="nav-form" onclick="launchCreateEventForm()">
                <svg viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 4.5v15m7.5-7.5h-15"/></svg>
                Add Event
            </button>
            <button class="nav-item" id="nav-profile" onclick="navigateTo('profile')">
                <svg viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15.75 6a3.75 3.75 0 11-7.5 0 3.75 3.75 0 017.5 0zM4.501 20.118a7.5 7.5 0 0114.998 0A17.933 17.933 0 0112 21.75c-2.676 0-5.216-.584-7.499-1.632z"/></svg>
                Profile
            </button>
        </nav>

    </div>

    <!-- Application Script Data Engine Pipeline -->
    <script>
        // --- State Configuration Store ---
        let appState = {
            user: {
                name: "Guest User",
                email: "",
                avatarData: null
            },
            currentScreen: "gate",
            activeFilter: "all", 
            registeredMembers: [], 
            events: [
                {
                    id: "evt-1",
                    title: "Downtown Farmers Market",
                    date: "2026-06-05T09:00",
                    desc: "Support local agricultural growers and crafts artisans. Bring your own reusable carrying bags!",
                    joined: true
                },
                {
                    id: "evt-2",
                    title: "Community Tree Planting Rally",
                    date: "2026-06-12T10:30",
                    desc: "Help canopy restoration efforts in the northern municipal park district. Free gloves, shovels, and snacks provided.",
                    joined: false
                }
            ]
        };

        // --- Screen Route Transitions ---
        function navigateTo(screenId) {
            appState.currentScreen = screenId;
            
            document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
            const targetScreen = document.getElementById(`screen-${screenId}`);
            if (targetScreen) targetScreen.classList.add('active');

            document.querySelectorAll('.flow-btn').forEach(b => b.classList.remove('active-flow'));
            const flowBtn = document.getElementById(`flow-${screenId}`);
            if (flowBtn) flowBtn.classList.add('active-flow');

            document.getElementById('scroll-container').scrollTop = 0;

            const titleElement = document.getElementById('view-title');
            document.getElementById('header-action-slot').innerHTML = ''; 

            if(screenId === 'gate') {
                titleElement.textContent = "Join Community";
                document.getElementById('global-nav').classList.add('nav-locked');
            } else {
                document.getElementById('global-nav').classList.remove('nav-locked');
                document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
                const targetedNavTab = document.getElementById(`nav-${screenId}`);
                if (targetedNavTab) targetedNavTab.classList.add('active');
            }

            if(screenId === 'dashboard') {
                titleElement.textContent = "Discover Events";
                renderEvents();
            } else if (screenId === 'members') {
                titleElement.textContent = "Neighbors Directory";
                renderMembers();
            } else if (screenId === 'form') {
                const isEditing = document.getElementById('edit-event-id').value !== "";
                titleElement.textContent = isEditing ? "Modify Event" : "Create Event";
            } else if (screenId === 'profile') {
                titleElement.textContent = "Your Profile";
                populateProfileForm();
            }
        }

        // --- Flow Navigation Control Simulator Bypass ---
        function devBypassRoute(flowKey) {
            if(flowKey !== 'gate' && appState.user.name === "Guest User") {
                appState.user.name = "Alex Morgan";
                appState.user.email = "alex.m@domain.com";
                if(!appState.registeredMembers.some(m => m.email === appState.user.email)) {
                    appState.registeredMembers.push({
                        name: appState.user.name,
                        email: appState.user.email,
                        avatar: appState.user.avatarData,
                        role: "Organizer"
                    });
                }
            }
            if(flowKey === 'form') {
                document.getElementById('event-mutation-form').reset();
                document.getElementById('edit-event-id').value = "";
            }
            navigateTo(flowKey);
        }

        // --- Toast Alerts Engine ---
        function displayToastAlert(message) {
            const container = document.getElementById('toast');
            container.textContent = message;
            container.style.display = 'block';
            setTimeout(() => { container.style.display = 'none'; }, 2500);
        }

        // --- Auth Controllers ---
        function handleSignup(event) {
            event.preventDefault();
            appState.user.name = document.getElementById('reg-name').value;
            appState.user.email = document.getElementById('reg-email').value;
            
            if(!appState.registeredMembers.some(m => m.email === appState.user.email)) {
                appState.registeredMembers.push({
                    name: appState.user.name,
                    email: appState.user.email,
                    avatar: appState.user.avatarData,
                    role: "New Neighbor"
                });
            }

            displayToastAlert(`Welcome, ${appState.user.name}!`);
            navigateTo('dashboard');
        }

        // --- Dashboard System Logic ---
        function setFilter(filterType) {
            appState.activeFilter = filterType;
            document.getElementById('filter-all').classList.toggle('active', filterType === 'all');
            document.getElementById('filter-joined').classList.toggle('active', filterType === 'joined');
            renderEvents();
        }

        function renderEvents() {
            const displayZone = document.getElementById('events-list-container');
            const contextualTitle = document.getElementById('list-context-title');
            displayZone.innerHTML = '';

            let querySet = appState.events;
            if (appState.activeFilter === 'joined') {
                querySet = appState.events.filter(e => e.joined);
                contextualTitle.textContent = `My Schedule (${querySet.length})`;
            } else {
                contextualTitle.textContent = "Upcoming events";
            }

            if(querySet.length === 0) {
                displayZone.innerHTML = `<div style="text-align:center; padding:40px 10px; color:var(--text-muted); font-size:0.9rem;">No events scheduled.</div>`;
                return;
            }

            querySet.forEach(item => {
                const card = document.createElement('div');
                card.className = 'card';
                card.onclick = () => toggledEventParticipation(item.id);

                const timeFormatted = new Date(item.date).toLocaleString([], {month: 'short', day: 'numeric', hour: '2-digit', minute:'2-digit'});

                card.innerHTML = `
                    <button class="card-edit-trigger" title="Modify Event Context" onclick="interceptFormEdit(event, '${item.id}')">
                        <svg width="14" height="14" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M16.862 4.487l1.687-1.688a1.875 1.875 0 112.652 2.652L6.832 19.82a4.5 4.5 0 01-1.897 1.13l-2.685.8.8-2.685a4.5 4.5 0 011.13-1.897L16.863 4.487zm0 0L19.5 7.125"/></svg>
                    </button>
                    <h3>${item.title}</h3>
                    <p>${item.desc}</p>
                    <div class="card-meta">
                        <span style="font-size: 0.8rem; font-weight:600; color: var(--primary);">${timeFormatted}</span>
                        <span class="badge ${item.joined ? 'joined' : ''}">${item.joined ? '✓ Attending' : 'Join Event'}</span>
                    </div>
                `;
                displayZone.appendChild(card);
            });
        }

        function toggledEventParticipation(id) {
            appState.events = appState.events.map(e => {
                if(e.id === id) {
                    const inversedState = !e.joined;
                    displayToastAlert(inversedState ? "Joined event schedule!" : "Removed from schedule.");
                    return { ...e, joined: inversedState };
                }
                return e;
            });
            renderEvents();
        }

        // --- Members Pipeline UI Sync ---
        function renderMembers() {
            const listContainer = document.getElementById('members-list-container');
            const countTitle = document.getElementById('members-count-title');
            listContainer.innerHTML = '';

            countTitle.textContent = `Registered Members (${appState.registeredMembers.length})`;

            if(appState.registeredMembers.length === 0) {
                listContainer.innerHTML = `<div class="empty-state"><p>No neighbors registered yet.</p></div>`;
                return;
            }

            appState.registeredMembers.forEach(member => {
                const row = document.createElement('div');
                row.className = 'member-row';

                let avatarLayoutHTML = `
                    <div class="member-avatar-container">
                        <svg width="22" height="22" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15.75 6a3.75 3.75 0 11-7.5 0 3.75 3.75 0 017.5 0zM4.501 20.118a7.5 7.5 0 0114.998 0A17.933 17.933 0 0112 21.75c-2.676 0-5.216-.584-7.499-1.632z"/></svg>
                    </div>`;
                
                if(member.avatar) {
                    avatarLayoutHTML = `<div class="member-avatar-container"><img src="${member.avatar}" alt="Member Profile Picture"></div>`;
                }

                row.innerHTML = `
                    ${avatarLayoutHTML}
                    <div class="member-info">
                        <h4>${member.name}</h4>
                        <p>${member.email}</p>
                    </div>
                    <span class="badge" style="background:#f1f5f9; color:#475569;">${member.role}</span>
                `;
                listContainer.appendChild(row);
            });
        }

        // --- Form Mutators ---
        function launchCreateEventForm() {
            document.getElementById('event-mutation-form').reset();
            document.getElementById('edit-event-id').value = "";
            document.getElementById('form-submit-btn').textContent = "Publish Event";
            navigateTo('form');
        }

        function interceptFormEdit(event, targetId) {
            event.stopPropagation(); 
            const dataMatch = appState.events.find(e => e.id === targetId);
            if(!dataMatch) return;

            document.getElementById('edit-event-id').value = dataMatch.id;
            document.getElementById('evt-title').value = dataMatch.title;
            document.getElementById('evt-date').value = dataMatch.date;
            document.getElementById('evt-desc').value = dataMatch.desc;
            
            document.getElementById('form-submit-btn').textContent = "Save Modifications";
            navigateTo('form');
        }

        function handleEventSubmit(event) {
            event.preventDefault();
            const referenceId = document.getElementById('edit-event-id').value;
            const contextPayload = {
                title: document.getElementById('evt-title').value,
                date: document.getElementById('evt-date').value,
                desc: document.getElementById('evt-desc').value
            };

            if(referenceId) {
                appState.events = appState.events.map(e => e.id === referenceId ? { ...e, ...contextPayload } : e);
                displayToastAlert("Event modified successfully!");
            } else {
                appState.events.unshift({
                    id: "evt-" + Date.now(),
                    ...contextPayload,
                    joined: true
                });
                displayToastAlert("New event published!");
            }
            navigateTo('dashboard');
        }

        // --- Profile Management & Live Avatar Upload Workflow ---
        function populateProfileForm() {
            document.getElementById('prof-name').value = appState.user.name;
            document.getElementById('prof-email').value = appState.user.email;
            syncAvatarDOMPreview();
        }

        function triggerAvatarSelection() {
            // Clicks hidden underlying form input programmatically
            document.getElementById('hidden-avatar-input').click();
        }

        function processAvatarUpload(event) {
            const binaryFile = event.target.files[0];
            if (!binaryFile) return;

            const trackingReader = new FileReader();
            trackingReader.onload = function (e) {
                // Transforms file element data chunk instantly into base64 raw string
                appState.user.avatarData = e.target.result;
                syncAvatarDOMPreview();
                displayToastAlert("Photo attached! Save profile below to commit changes.");
            }
            trackingReader.readAsDataURL(binaryFile);
        }

        function syncAvatarDOMPreview() {
            const visualRender = document.getElementById('avatar-img');
            const promptSvg = document.getElementById('avatar-hint-svg');

            if (appState.user.avatarData) {
                visualRender.src = appState.user.avatarData;
                visualRender.style.display = 'block';
                promptSvg.style.display = 'none';
            } else {
                promptSvg.style.display = 'block';
                visualRender.style.display = 'none';
            }
        }

        function handleProfileUpdate(event) {
            event.preventDefault();
            const oldEmail = appState.user.email;
            appState.user.name = document.getElementById('prof-name').value;
            appState.user.email = document.getElementById('prof-email').value;
            
            // Sync database reference values back to members state data object list structure
            appState.registeredMembers = appState.registeredMembers.map(m => {
                if (m.email === oldEmail || m.role === "New Neighbor" || m.role === "Organizer") {
                    return { ...m, name: appState.user.name, email: appState.user.email, avatar: appState.user.avatarData };
                }
                return m;
            });

            displayToastAlert("Profile saved and directory data synced!");
            navigateTo('dashboard');
        }

        window.addEventListener('DOMContentLoaded', () => {
            renderEvents();
        });
    </script>
</body>
</html>

```
