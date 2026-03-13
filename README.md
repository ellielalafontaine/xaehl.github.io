<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>XAEHL S4 Stats</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.4.1/papaparse.min.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Teko:wght@300;400;500;600;700&family=Rajdhani:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: 'Rajdhani', sans-serif;
            background: #d9d9d9;
            color: #1a1a1a;
            padding: 20px;
            min-height: 100vh;
        }
        .container { max-width: 1600px; margin: 0 auto; }
        .header {
            text-align: center;
            margin-bottom: 20px;
            padding: 30px;
            background: white;
            border: 4px solid #01b8bf;
            box-shadow: 8px 8px 0px rgba(1, 184, 191, 0.3);
        }
        h1 {
            font-family: 'Teko', sans-serif;
            font-size: 5rem;
            color: #01b8bf;
            font-weight: 700;
            letter-spacing: 12px;
            text-transform: uppercase;
        }
        .season {
            font-family: 'Teko', sans-serif;
            font-size: 2.5rem;
            color: #1a1a1a;
            font-weight: 600;
            letter-spacing: 4px;
            text-transform: uppercase;
        }
        .subtitle {
            font-size: 1rem;
            color: #666;
            font-weight: 500;
            letter-spacing: 2px;
            text-transform: uppercase;
        }
        .controls {
            display: flex;
            gap: 15px;
            justify-content: center;
            align-items: center;
            margin-bottom: 30px;
            flex-wrap: wrap;
            padding: 20px;
            background: white;
            border: 4px solid #01b8bf;
            box-shadow: 6px 6px 0px rgba(1, 184, 191, 0.2);
        }
        .tab-button {
            padding: 12px 24px;
            font-size: 1rem;
            border: 3px solid #01b8bf;
            background: white;
            cursor: pointer;
            color: #1a1a1a;
            font-family: 'Rajdhani', sans-serif;
            font-weight: 700;
            text-transform: uppercase;
        }
        .tab-button:hover { background: #01b8bf; color: white; }
        .tab-button.active { background: #01b8bf; color: white; }
        .mvp-section {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }
        .mvp-card {
            background: white;
            padding: 20px;
            border: 4px solid #01b8bf;
            box-shadow: 6px 6px 0px rgba(1, 184, 191, 0.3);
            text-align: center;
        }
        .mvp-title {
            font-family: 'Teko', sans-serif;
            font-size: 1.1rem;
            font-weight: 700;
            color: #1a1a1a;
            text-transform: uppercase;
            letter-spacing: 2px;
        }
        .mvp-player { font-size: 1.4rem; font-weight: 700; color: #01b8bf; }
        .mvp-stat { font-size: 1.8rem; font-weight: 700; color: #1a1a1a; }
        .mvp-team { font-size: 0.85rem; color: #666; text-transform: uppercase; }
        .loading {
            text-align: center;
            font-size: 1.8rem;
            color: #01b8bf;
            padding: 60px 20px;
            font-weight: 700;
            text-transform: uppercase;
            font-family: 'Teko', sans-serif;
        }
        .table-container {
            overflow-x: auto;
            border: 4px solid #01b8bf;
            box-shadow: 8px 8px 0px rgba(1, 184, 191, 0.2);
            margin-bottom: 30px;
            background: white;
        }
        table { width: 100%; border-collapse: collapse; background: white; }
        thead { background: #01b8bf; position: sticky; top: 0; z-index: 10; }
        th {
            padding: 16px 12px;
            text-align: left;
            font-weight: 700;
            color: white;
            cursor: pointer;
            text-transform: uppercase;
            font-size: 0.9rem;
            user-select: none;
            position: relative;
        }
        th:hover { background: #019da3; }
        th.sortable::after {
            content: ' ↕';
            opacity: 0.3;
            font-size: 0.8rem;
        }
        th.sorted-asc::after {
            content: ' ↑';
            opacity: 1;
        }
        th.sorted-desc::after {
            content: ' ↓';
            opacity: 1;
        }
        td { padding: 14px 12px; color: #1a1a1a; font-weight: 500; }
        tr { border-bottom: 2px solid #e5e7eb; }
        tbody tr:hover { background: #e6f9fa; }
        .text-center { text-align: center; }
        .highlight { font-weight: 700; color: #01b8bf; }
        .per-game { font-size: 0.85rem; color: #666; font-weight: 600; }
        .team-name { font-weight: 700; font-size: 1.05rem; }
        .record-good { color: #10b981; font-weight: 700; }
        .record-bad { color: #ef4444; font-weight: 700; }
        .info-banner {
            background: white;
            border: 3px solid #01b8bf;
            border-left: 8px solid #01b8bf;
            padding: 15px 20px;
            margin-bottom: 20px;
            font-weight: 600;
            text-transform: uppercase;
        }
        .footer {
            text-align: center;
            margin-top: 30px;
            padding: 20px;
            color: #666;
            font-weight: 600;
            background: white;
            border: 3px solid #01b8bf;
        }
        .footer a {
            color: #666;
            text-decoration: none;
            font-weight: 600;
        }
        .footer a:hover {
            color: #01b8bf;
            text-decoration: underline;
        }
        .toggle-container {
            display: flex;
            gap: 10px;
            align-items: center;
            margin-bottom: 15px;
        }
        .toggle-label {
            font-weight: 700;
            color: #1a1a1a;
            text-transform: uppercase;
            font-size: 0.9rem;
        }
        .toggle-button {
            padding: 8px 16px;
            font-size: 0.9rem;
            border: 3px solid #01b8bf;
            background: white;
            cursor: pointer;
            color: #1a1a1a;
            font-family: 'Rajdhani', sans-serif;
            font-weight: 700;
            text-transform: uppercase;
        }
        .toggle-button:hover { background: #e6f9fa; }
        .toggle-button.active { background: #01b8bf; color: white; }
        .h2h-matrix { font-size: 0.85rem; }
        .h2h-matrix th, .h2h-matrix td { padding: 10px 8px; }
        .h2h-win { background: rgba(16, 185, 129, 0.15); font-weight: 700; color: #10b981; }
        .h2h-loss { background: rgba(239, 68, 68, 0.15); font-weight: 700; color: #ef4444; }
        .h2h-tie { background: rgba(107, 114, 128, 0.15); font-weight: 700; color: #6b7280; }
        .h2h-none { background: rgba(209, 213, 219, 0.3); color: #9ca3af; }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>XAEHL</h1>
            <div class="season">Season 4</div>
            <div class="subtitle">Fansite ran by Jaromir Jagwood</div>
        </div>
        
        <div class="controls">
            <button class="tab-button active" data-tab="players">📈 Players</button>
            <button class="tab-button" data-tab="goalies">🥅 Goalies</button>
            <button class="tab-button" data-tab="teams">👥 Teams</button>
            <button class="tab-button" data-tab="standings">🏆 Standings</button>
            <button class="tab-button" data-tab="schedule">📅 Schedule</button>
            <button class="tab-button" data-tab="headtohead">⚔️ Head-to-Head</button>
            <button class="refresh-btn tab-button" onclick="location.reload()">↻ Refresh</button>
        </div>
        
        <div id="info-banner"></div>
        <div id="mvp-container"></div>
        <div id="content"><div class="loading">Loading Season 4 Stats...</div></div>
        <div class="footer">
            <span id="stats-info"></span>
            <span style="margin: 0 10px;">|</span>
            <a href="https://xaehls3.edgeone.dev/" target="_blank" rel="noopener noreferrer">Season 3 Archive</a>
            <span style="margin: 0 10px;">|</span>
            <a href="https://xaechl.edgeone.dev/" target="_blank" rel="noopener noreferrer">Minor League (CHL)</a>
        </div>
    </div>

    <script>
        const PLAYER_STATS_CSV_URL = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTE-dBMqLlpGk5mZYpym7s-zgCYFOTilJJmaYUHeu-o0u1QUjthHifu_gFjPHK9Sh48z0UAd0jJK06G/pub?gid=0&single=true&output=csv';
        const TEAM_STATS_CSV_URL = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTE-dBMqLlpGk5mZYpym7s-zgCYFOTilJJmaYUHeu-o0u1QUjthHifu_gFjPHK9Sh48z0UAd0jJK06G/pub?gid=1750367121&single=true&output=csv';
        
        const SCHEDULE = [
            // Week 1
            { game: 1,  week: 1, day: 'Thursday', home: "Crilley's Clergy",    away: "Les Cities D'Or" },
            { game: 2,  week: 1, day: 'Thursday', home: 'Pissy Shitties',       away: 'The Wildlings' },
            { game: 3,  week: 1, day: 'Saturday', home: 'Pyroclastic Flow',     away: 'Scunthorpe Industrial' },
            { game: 4,  week: 1, day: 'Saturday', home: 'Cottonwood Cobros',    away: 'Black Wizards' },
            { game: 5,  week: 1, day: 'Saturday', home: 'Crumble Mutants',      away: 'Zodiac Jaggernauts' },

            // Week 2
            { game: 6,  week: 2, day: 'Wednesday', home: "Crilley's Clergy",   away: 'The Wildlings' },
            { game: 7,  week: 2, day: 'Wednesday', home: "Les Cities D'Or",    away: 'Scunthorpe Industrial' },
            { game: 8,  week: 2, day: 'Saturday',  home: 'Pissy Shitties',     away: 'Black Wizards' },
            { game: 9,  week: 2, day: 'Saturday',  home: 'Pyroclastic Flow',   away: 'Zodiac Jaggernauts' },
            { game: 10, week: 2, day: 'Saturday',  home: 'Cottonwood Cobros',  away: 'Crumble Mutants' },

            // Week 3
            { game: 11, week: 3, day: 'Wednesday', home: "Crilley's Clergy",   away: 'Scunthorpe Industrial' },
            { game: 12, week: 3, day: 'Wednesday', home: 'The Wildlings',      away: 'Black Wizards' },
            { game: 13, week: 3, day: 'Saturday',  home: "Les Cities D'Or",   away: 'Zodiac Jaggernauts' },
            { game: 14, week: 3, day: 'Saturday',  home: 'Pissy Shitties',    away: 'Cottonwood Cobros' },
            { game: 15, week: 3, day: 'Saturday',  home: 'Pyroclastic Flow',  away: 'Crumble Mutants' },

            // Week 4
            { game: 16, week: 4, day: 'Wednesday', home: "Crilley's Clergy",   away: 'Black Wizards' },
            { game: 17, week: 4, day: 'Wednesday', home: 'Scunthorpe Industrial', away: 'Zodiac Jaggernauts' },
            { game: 18, week: 4, day: 'Saturday',  home: 'The Wildlings',      away: 'Cottonwood Cobros' },
            { game: 19, week: 4, day: 'Saturday',  home: "Les Cities D'Or",   away: 'Pyroclastic Flow' },
            { game: 20, week: 4, day: 'Saturday',  home: 'Pissy Shitties',    away: 'Crumble Mutants' },

            // Week 5
            { game: 21, week: 5, day: 'Wednesday', home: "Crilley's Clergy",   away: 'Zodiac Jaggernauts' },
            { game: 22, week: 5, day: 'Wednesday', home: 'Black Wizards',      away: 'Crumble Mutants' },
            { game: 23, week: 5, day: 'Saturday',  home: 'Scunthorpe Industrial', away: 'Cottonwood Cobros' },
            { game: 24, week: 5, day: 'Saturday',  home: 'The Wildlings',     away: 'Pyroclastic Flow' },
            { game: 25, week: 5, day: 'Saturday',  home: "Les Cities D'Or",   away: 'Pissy Shitties' },

            // Week 6
            { game: 26, week: 6, day: 'Wednesday', home: "Crilley's Clergy",   away: 'Crumble Mutants' },
            { game: 27, week: 6, day: 'Wednesday', home: 'Zodiac Jaggernauts', away: 'Cottonwood Cobros' },
            { game: 28, week: 6, day: 'Saturday',  home: 'Black Wizards',     away: 'Pyroclastic Flow' },
            { game: 29, week: 6, day: 'Saturday',  home: 'Scunthorpe Industrial', away: 'Pissy Shitties' },
            { game: 30, week: 6, day: 'Saturday',  home: 'The Wildlings',     away: "Les Cities D'Or" },

            // Week 7
            { game: 31, week: 7, day: 'Wednesday', home: "Crilley's Clergy",   away: 'Cottonwood Cobros' },
            { game: 32, week: 7, day: 'Wednesday', home: 'Pissy Shitties',    away: 'Pyroclastic Flow' },
            { game: 33, week: 7, day: 'Saturday',  home: 'Zodiac Jaggernauts', away: 'The Wildlings' },
            { game: 34, week: 7, day: 'Saturday',  home: 'Black Wizards',     away: "Les Cities D'Or" },
            { game: 35, week: 7, day: 'Saturday',  home: 'Crumble Mutants',   away: 'Scunthorpe Industrial' },

            // Week 8
            { game: 36, week: 8, day: 'Wednesday', home: "Crilley's Clergy",   away: 'Pyroclastic Flow' },
            { game: 37, week: 8, day: 'Wednesday', home: 'Pissy Shitties',    away: 'Zodiac Jaggernauts' },
            { game: 38, week: 8, day: 'Saturday',  home: "Les Cities D'Or",   away: 'Cottonwood Cobros' },
            { game: 39, week: 8, day: 'Saturday',  home: 'Crumble Mutants',   away: 'The Wildlings' },
            { game: 40, week: 8, day: 'Saturday',  home: 'Black Wizards',     away: 'Scunthorpe Industrial' },

            // Week 9
            { game: 41, week: 9, day: 'Wednesday', home: "Crilley's Clergy",   away: 'Pissy Shitties' },
            { game: 42, week: 9, day: 'Wednesday', home: 'Pyroclastic Flow',   away: 'Cottonwood Cobros' },
            { game: 43, week: 9, day: 'Saturday',  home: 'The Wildlings',     away: 'Scunthorpe Industrial' },
            { game: 44, week: 9, day: 'Saturday',  home: 'Crumble Mutants',   away: "Les Cities D'Or" },
            { game: 45, week: 9, day: 'Saturday',  home: 'Zodiac Jaggernauts', away: 'Black Wizards' }
        ];
        
        const TEAM_COLORS = {
            'Wildlings': 'rgba(59, 130, 246, 0.12)',
            'The Wildlings': 'rgba(59, 130, 246, 0.12)',
            'Les Cities D\'Or': 'rgba(234, 179, 8, 0.12)',
            'Les Cities D\'or': 'rgba(234, 179, 8, 0.12)',
            'Scunthorpe Industrial': 'rgba(185, 28, 28, 0.12)',
            'Crilley\'s Clergy': 'rgba(30, 30, 30, 0.08)',
            'Crumble Mutants': 'rgba(161, 98, 7, 0.12)',
            'Pissy Shitties': 'rgba(202, 138, 4, 0.15)',
            'Black Wizards': 'rgba(147, 51, 234, 0.12)',
            'Cottonwood Cobros': 'rgba(34, 197, 94, 0.12)',
            'Pyroclastic Flow': 'rgba(239, 68, 68, 0.12)',
            'Zodiac Jaggernauts': 'rgba(132, 204, 22, 0.15)'
        };
        
        const GOALIES = {
            'Pyroclastic Flow': 'Seamus Aodagain',
            'The Wildlings': 'Dar Boulder',
            'Les Cities D\'Or': 'Mendoza Esperanza',
            'Scunthorpe Industrial': 'Alastair Journey',
            'Zodiac Jaggernauts': 'Jaromir Jagwood',
            'Black Wizards': 'Johnny Trym',
            'Crumble Mutants': 'Gurgle Bunkis',
            'Pissy Shitties': 'Leonardo DiCrapio',
            'Crilley\'s Clergy': 'James Bearden',
            'Cottonwood Cobros': 'Stacey Stool'
        };
        
        const DEFAULT_FACTIONS = {
            'Death From Above': ['Crilley\'s Clergy', 'Colosseum Crushers', 'Zodiac Jaggernauts'],
            'Richmond Scrappers': ['Cottonwood Cobros'],
            'Northern Union': ['Crumble Mutants'],
            'Gur Remnant': ['Pissy Shitties', 'Black Wizards'],
            'Halmut Orthodoxy': ['Pyroclastic Flow']
        };
        
        let FACTIONS = DEFAULT_FACTIONS;
        let allData = { players: [], teams: {}, goalies: [], rawPlayerData: [], rawTeamData: [] };
        let currentTab = 'players';
        let sortConfig = { key: 'PTS', direction: 'desc' };
        let showPerGame = false;
        
        function sortTable(key, dataArray) {
            if (sortConfig.key === key) {
                sortConfig.direction = sortConfig.direction === 'asc' ? 'desc' : 'asc';
            } else {
                sortConfig.key = key;
                sortConfig.direction = 'desc';
            }
            
            dataArray.sort((a, b) => {
                let aVal = a[key];
                let bVal = b[key];
                
                if (typeof aVal === 'string') {
                    aVal = aVal.toLowerCase();
                    bVal = bVal.toLowerCase();
                    return sortConfig.direction === 'asc' 
                        ? aVal.localeCompare(bVal)
                        : bVal.localeCompare(aVal);
                }
                
                return sortConfig.direction === 'asc' ? aVal - bVal : bVal - aVal;
            });
        }
        
        function getTeamColor(team) {
            return TEAM_COLORS[team] || 'transparent';
        }
        
        const footerMessages = [
            'Fresh Start', 'Expansion Season!', 'Down with the CCPC!',
            'Did anyone ever find Sally Sleuthy?', 'Whatever happened to the Colosseum Crushers?',
            'Has anyone seen my vape?', 'Jagwood\'s Fan Corner', 'Unauthorized Statistics',
            'Not affiliated with the CCPC', 'Season 4 | The Return',
            'Who started the stick plague?', 'Where\'s Daphne at?',
            'What happened to the Catastrophe Bureau?', 'What are they doing on Beak Peak?',
            'Halmut is a nice guy, I swear', 'They just won\'t stop shitting!',
            'Brought to you from our home town rink: The Toilet Bowl',
            'Scrappy, and sure not rich',
            'I had a nightmare about Jaromir Jagwater last night',
            'I think the cultists poisoned my seltzer water',
            'I love Richmond but their water is so oily',
            'Now that I think of it, I\'ve never seen a snake on XAE',
            'These industrial boys sure are weird',
            'I feel bad for the Crumble Mutants. They\'re so.. Mutated',
            'Je ne sais pa?',
            'I wonder if Barry and Barry will ever be reunited',
            'Znake stole my vape!',
            'Bacontrim is my hero',
            'Why is it always so hot at Flow home games?'
        ];
        const randomFooter = footerMessages[Math.floor(Math.random() * footerMessages.length)];
        
        document.querySelectorAll('.tab-button').forEach(btn => {
            btn.addEventListener('click', (e) => {
                if (e.target.classList.contains('refresh-btn')) return;
                document.querySelectorAll('.tab-button').forEach(b => b.classList.remove('active'));
                e.target.classList.add('active');
                currentTab = e.target.dataset.tab;
                renderCurrentView();
            });
        });
        
        async function fetchData() {
            try {
                const playerResponse = await fetch(PLAYER_STATS_CSV_URL);
                if (!playerResponse.ok) throw new Error('Could not fetch player data');
                const playerCsv = await playerResponse.text();
                
                let teamCsv = null;
                try {
                    const teamResponse = await fetch(TEAM_STATS_CSV_URL);
                    if (teamResponse.ok) teamCsv = await teamResponse.text();
                } catch (e) {}
                
                Papa.parse(playerCsv, {
                    header: true,
                    skipEmptyLines: true,
                    complete: function(results) {
                        allData.rawPlayerData = results.data;
                        if (teamCsv) {
                            Papa.parse(teamCsv, {
                                header: true,
                                skipEmptyLines: true,
                                complete: function(teamResults) {
                                    allData.rawTeamData = teamResults.data;
                                    processAllData();
                                    renderCurrentView();
                                }
                            });
                        } else {
                            processAllData();
                            renderCurrentView();
                        }
                    }
                });
            } catch (error) {
                document.getElementById('content').innerHTML = '<div class="loading">Error loading data</div>';
                document.getElementById('stats-info').textContent = randomFooter;
            }
        }
        
        function processAllData() {
            const playerMap = {};
            allData.rawPlayerData.forEach(row => {
                let player = String(row.Player || row.PLAYER || '').trim();
                let team = String(row.Team || row.TEAM || '').trim();
                if (!player || !team || player.toLowerCase() === 'player') return;
                
                if (team === 'Les Cities D\'or') team = 'Les Cities D\'Or';
                if (team === 'Wildlings') team = 'The Wildlings';
                
                const key = `${player}|${team}`;
                if (!playerMap[key]) {
                    playerMap[key] = { Player: player, Team: team, GP: 0, G: 0, A: 0, PTS: 0, Shots: 0, Passes: 0, Hits: 0, Int: 0, Blk: 0 };
                }
                const p = playerMap[key];
                p.GP += 1;
                p.G += parseInt(row.G) || 0;
                p.A += parseInt(row.A) || 0;
                p.Shots += parseInt(row.Shots || row.SHOTS) || 0;
                p.Passes += parseInt(row.Passes || row.PASSES) || 0;
                p.Hits += parseInt(row.Hits || row.HITS) || 0;
                p.Int += parseInt(row.Int || row.INT) || 0;
                p.Blk += parseInt(row.Blk || row.BLK) || 0;
                p.PTS = p.G + p.A;
            });
            allData.players = Object.values(playerMap);
            
            const teamMap = {};
            const gameResults = {};
            
            if (allData.rawTeamData.length > 0) {
                allData.rawTeamData.forEach(row => {
                    const game = parseInt(row.Game || row.GAME) || 0;
                    let team = String(row.Team || row.TEAM || '').trim();
                    const score = parseInt(row.Score || row.SCORE) || 0;
                    const winType = String(row.WinType || row.WINTYPE || 'REG').trim().toUpperCase();
                    if (!team || game === 0) return;
                    
                    if (team === 'Les Cities D\'or') team = 'Les Cities D\'Or';
                    if (team === 'Wildlings') team = 'The Wildlings';
                    
                    if (!gameResults[game]) gameResults[game] = [];
                    gameResults[game].push({ team, score, winType });
                });
                
                allData.rawTeamData.forEach(row => {
                    const game = parseInt(row.Game || row.GAME) || 0;
                    let team = String(row.Team || row.TEAM || '').trim();
                    if (!team) return;
                    
                    if (team === 'Les Cities D\'or') team = 'Les Cities D\'Or';
                    if (team === 'Wildlings') team = 'The Wildlings';
                    
                    if (!teamMap[team]) {
                        teamMap[team] = { 
                            GP: 0, W: 0, L: 0, OTW: 0, OTL: 0, PTS: 0, RW: 0, GF: 0, GA: 0, 
                            PassPct: 0, PassPctSum: 0, PassPctCount: 0,
                            TimeSum: 0, TimeCount: 0, FaceoffSum: 0
                        };
                    }
                    const t = teamMap[team];
                    const score = parseInt(row.Score || row.SCORE) || 0;
                    const winType = String(row.WinType || row.WINTYPE || 'REG').trim().toUpperCase();
                    
                    t.GP += 1;
                    t.GF += score;
                    
                    const passPct = parseFloat(row['Passes%'] || row['PASSES%'] || '0') || 0;
                    if (passPct > 0) {
                        t.PassPctSum += passPct;
                        t.PassPctCount += 1;
                    }
                    
                    const timeOnAttack = String(row.TimeOnAttack || row.TIMEONATTACK || '0:00').trim();
                    const timeParts = timeOnAttack.split(':');
                    if (timeParts.length === 2) {
                        const mins = parseInt(timeParts[0]) || 0;
                        const secs = parseInt(timeParts[1]) || 0;
                        t.TimeSum += (mins * 60 + secs);
                        t.TimeCount += 1;
                    }
                    
                    const faceoffs = parseInt(row.FaceoffWon || row.FACEOFFWON) || 0;
                    if (faceoffs > 0) {
                        t.FaceoffSum += faceoffs;
                    }
                    
                    const gameData = gameResults[game] || [];
                    if (gameData.length === 2) {
                        const teamData = gameData.find(g => g.team === team);
                        const opponentData = gameData.find(g => g.team !== team);
                        if (teamData && opponentData) {
                            t.GA += opponentData.score;
                            if (teamData.score > opponentData.score) {
                                t.W += 1;
                                if (teamData.winType === 'REG') { t.PTS += 3; t.RW += 1; }
                                else { t.PTS += 2; t.OTW += 1; }
                            } else {
                                if (opponentData.winType === 'OT' || opponentData.winType === 'SO') { 
                                    t.OTL += 1; t.PTS += 1; 
                                }
                                else { t.L += 1; }
                            }
                        }
                    }
                });
            }
            
            allData.teams = aggregateTeamStats(allData.players, teamMap);
            allData.goalies = calculateGoalieStats(teamMap, gameResults);
        }
        
        function getTeamFaction(team) {
            for (const [faction, teams] of Object.entries(FACTIONS)) {
                if (teams.includes(team)) return faction;
            }
            return 'Unknown';
        }
        
        function aggregateTeamStats(players, teamRecords = {}) {
            const teams = {};
            players.forEach(p => {
                if (!teams[p.Team]) {
                    teams[p.Team] = { 
                        Team: p.Team, 
                        Faction: getTeamFaction(p.Team), 
                        GP: 0, W: 0, L: 0, OTW: 0, OTL: 0, PTS: 0, RW: 0, 
                        GF: 0, GA: 0, G: 0, A: 0, TotalPTS: 0, 
                        Shots: 0, Passes: 0, Hits: 0, Int: 0, Blk: 0,
                        PassPct: '0.0', TimeOnAttack: '00:00', FaceoffWon: 0,
                        PassPctSum: 0, PassPctCount: 0, TimeSum: 0, TimeCount: 0, FaceoffSum: 0
                    };
                }
                const t = teams[p.Team];
                t.GP = Math.max(t.GP, p.GP);
                t.G += p.G;
                t.A += p.A;
                t.TotalPTS += p.PTS;
                t.Shots += p.Shots;
                t.Passes += p.Passes;
                t.Hits += p.Hits;
                t.Int += p.Int;
                t.Blk += p.Blk;
            });
            
            Object.keys(teams).forEach(teamName => {
                if (teamRecords[teamName]) {
                    const tr = teamRecords[teamName];
                    teams[teamName].GP = tr.GP || teams[teamName].GP;
                    teams[teamName].W = tr.W || 0;
                    teams[teamName].L = tr.L || 0;
                    teams[teamName].OTW = tr.OTW || 0;
                    teams[teamName].OTL = tr.OTL || 0;
                    teams[teamName].PTS = tr.PTS || 0;
                    teams[teamName].RW = tr.RW || 0;
                    teams[teamName].GF = tr.GF || 0;
                    teams[teamName].GA = tr.GA || 0;
                    if (tr.PassPctCount > 0) {
                        teams[teamName].PassPct = (tr.PassPctSum / tr.PassPctCount).toFixed(1);
                    }
                    if (tr.TimeCount > 0) {
                        const avgSeconds = Math.round(tr.TimeSum / tr.TimeCount);
                        const mins = Math.floor(avgSeconds / 60);
                        const secs = avgSeconds % 60;
                        teams[teamName].TimeOnAttack = `${mins}:${secs.toString().padStart(2, '0')}`;
                    }
                    if (tr.FaceoffSum > 0) {
                        teams[teamName].FaceoffWon = Math.round(tr.FaceoffSum / tr.GP);
                    }
                }
            });
            return teams;
        }
        
        function calculateGoalieStats(teamMap, gameResults) {
            const goalies = [];
            
            const shotsAgainst = {};
            Object.keys(gameResults).forEach(gameNum => {
                const game = gameResults[gameNum];
                if (game.length === 2) {
                    const team1 = game[0].team;
                    const team2 = game[1].team;
                    
                    const team1Shots = allData.rawPlayerData
                        .filter(row => parseInt(row.Game || row.GAME) === parseInt(gameNum) && 
                                      (String(row.Team || row.TEAM).trim() === team1 || 
                                       (team1 === 'Les Cities D\'Or' && String(row.Team || row.TEAM).trim() === 'Les Cities D\'or') ||
                                       (team1 === 'The Wildlings' && String(row.Team || row.TEAM).trim() === 'Wildlings')))
                        .reduce((sum, row) => sum + (parseInt(row.Shots || row.SHOTS) || 0), 0);
                    
                    const team2Shots = allData.rawPlayerData
                        .filter(row => parseInt(row.Game || row.GAME) === parseInt(gameNum) && 
                                      (String(row.Team || row.TEAM).trim() === team2 || 
                                       (team2 === 'Les Cities D\'Or' && String(row.Team || row.TEAM).trim() === 'Les Cities D\'or') ||
                                       (team2 === 'The Wildlings' && String(row.Team || row.TEAM).trim() === 'Wildlings')))
                        .reduce((sum, row) => sum + (parseInt(row.Shots || row.SHOTS) || 0), 0);
                    
                    if (!shotsAgainst[team1]) shotsAgainst[team1] = 0;
                    if (!shotsAgainst[team2]) shotsAgainst[team2] = 0;
                    
                    shotsAgainst[team1] += team2Shots;
                    shotsAgainst[team2] += team1Shots;
                }
            });
            
            Object.keys(teamMap).forEach(teamName => {
                const team = teamMap[teamName];
                const goalieName = GOALIES[teamName] || `${teamName} Goalie`;
                
                const sa = shotsAgainst[teamName] || 0;
                const ga = team.GA || 0;
                const sv = sa - ga;
                const svPct = sa > 0 ? ((sv / sa) * 100).toFixed(1) : '0.0';
                const gaa = team.GP > 0 ? (ga / team.GP).toFixed(2) : '0.00';
                
                goalies.push({
                    Goalie: goalieName,
                    Team: teamName,
                    GP: team.GP,
                    W: team.W,
                    L: team.L,
                    OTW: team.OTW,
                    OTL: team.OTL,
                    GA: ga,
                    SA: sa,
                    SV: sv,
                    'SV%': parseFloat(svPct),
                    GAA: parseFloat(gaa)
                });
            });
            
            return goalies;
        }
        
        function renderCurrentView() {
            if (currentTab === 'players') renderPlayerStats();
            else if (currentTab === 'goalies') renderGoalieStats();
            else if (currentTab === 'teams') renderTeamStats();
            else if (currentTab === 'standings') renderStandings();
            else if (currentTab === 'schedule') renderSchedule();
            else if (currentTab === 'headtohead') renderHeadToHead();
            renderMVPs();
            document.getElementById('stats-info').textContent = randomFooter;
        }
        
        function renderMVPs() {
            if (currentTab === 'goalies') {
                if (allData.goalies.length === 0) {
                    document.getElementById('mvp-container').innerHTML = '';
                    return;
                }
                const goalies = allData.goalies;
                const bestSavePct = [...goalies].sort((a, b) => b['SV%'] - a['SV%'])[0];
                const lowestGAA = [...goalies].sort((a, b) => a.GAA - b.GAA)[0];
                const mostWins = [...goalies].sort((a, b) => b.W - a.W)[0];
                const mostSaves = [...goalies].sort((a, b) => b.SV - a.SV)[0];
                const bestRecord = [...goalies].filter(g => g.GP > 0).sort((a, b) => {
                    const aWinPct = a.W / a.GP;
                    const bWinPct = b.W / b.GP;
                    return bWinPct - aWinPct;
                })[0];
                
                document.getElementById('mvp-container').innerHTML = `
                    <div class="mvp-section">
                        <div class="mvp-card"><div class="mvp-title">🥇 Best Save %</div><div class="mvp-player">${bestSavePct.Goalie}</div><div class="mvp-stat">${bestSavePct['SV%']}%</div><div class="mvp-team">${bestSavePct.Team}</div></div>
                        <div class="mvp-card"><div class="mvp-title">🛡️ Lowest GAA</div><div class="mvp-player">${lowestGAA.Goalie}</div><div class="mvp-stat">${lowestGAA.GAA}</div><div class="mvp-team">${lowestGAA.Team}</div></div>
                        <div class="mvp-card"><div class="mvp-title">🏆 Most Wins</div><div class="mvp-player">${mostWins.Goalie}</div><div class="mvp-stat">${mostWins.W} W</div><div class="mvp-team">${mostWins.Team}</div></div>
                        <div class="mvp-card"><div class="mvp-title">💪 Most Saves</div><div class="mvp-player">${mostSaves.Goalie}</div><div class="mvp-stat">${mostSaves.SV} SV</div><div class="mvp-team">${mostSaves.Team}</div></div>
                        <div class="mvp-card"><div class="mvp-title">🔥 Best Record</div><div class="mvp-player">${bestRecord.Goalie}</div><div class="mvp-stat">${bestRecord.W}-${bestRecord.L}-${bestRecord.OTL}</div><div class="mvp-team">${bestRecord.Team}</div></div>
                    </div>
                `;
            } else if (currentTab === 'players') {
                if (allData.players.length === 0) {
                    document.getElementById('mvp-container').innerHTML = '';
                    return;
                }
                const players = allData.players;
                const pointsLeader = [...players].sort((a, b) => b.PTS - a.PTS)[0];
                const goalsLeader = [...players].sort((a, b) => b.G - a.G)[0];
                const assistsLeader = [...players].sort((a, b) => b.A - a.A)[0];
                const shotsLeader = [...players].sort((a, b) => b.Shots - a.Shots)[0];
                const defensiveLeader = [...players].sort((a, b) => (b.Blk + b.Int) - (a.Blk + a.Int))[0];
                
                document.getElementById('mvp-container').innerHTML = `
                    <div class="mvp-section">
                        <div class="mvp-card"><div class="mvp-title">🏆 Points Leader</div><div class="mvp-player">${pointsLeader.Player}</div><div class="mvp-stat">${pointsLeader.PTS} PTS</div><div class="mvp-team">${pointsLeader.Team}</div></div>
                        <div class="mvp-card"><div class="mvp-title">🎯 Top Scorer</div><div class="mvp-player">${goalsLeader.Player}</div><div class="mvp-stat">${goalsLeader.G} G</div><div class="mvp-team">${goalsLeader.Team}</div></div>
                        <div class="mvp-card"><div class="mvp-title">🎁 Playmaker</div><div class="mvp-player">${assistsLeader.Player}</div><div class="mvp-stat">${assistsLeader.A} A</div><div class="mvp-team">${assistsLeader.Team}</div></div>
                        <div class="mvp-card"><div class="mvp-title">💥 Shot Volume</div><div class="mvp-player">${shotsLeader.Player}</div><div class="mvp-stat">${shotsLeader.Shots} Shots</div><div class="mvp-team">${shotsLeader.Team}</div></div>
                        <div class="mvp-card"><div class="mvp-title">🛡️ Defensive Star</div><div class="mvp-player">${defensiveLeader.Player}</div><div class="mvp-stat">${defensiveLeader.Blk + defensiveLeader.Int} Def</div><div class="mvp-team">${defensiveLeader.Team}</div></div>
                    </div>
                `;
            } else {
                document.getElementById('mvp-container').innerHTML = '';
            }
        }
        
        function renderPlayerStats() {
            if (allData.players.length === 0) {
                document.getElementById('content').innerHTML = '<div class="loading">No player data</div>';
                return;
            }
            const sorted = [...allData.players];
            sortTable(sortConfig.key, sorted);
            
            let html = '<div class="toggle-container">';
            html += '<span class="toggle-label">View:</span>';
            html += `<button class="toggle-button ${!showPerGame ? 'active' : ''}" onclick="showPerGame = false; renderCurrentView()">Totals</button>`;
            html += `<button class="toggle-button ${showPerGame ? 'active' : ''}" onclick="showPerGame = true; renderCurrentView()">Per Game</button>`;
            html += '</div>';
            
            html += '<div class="table-container"><table><thead><tr>';
            
            if (showPerGame) {
                ['Player', 'Team', 'Games', 'Goals/Game', 'Assists/Game', 'Points/Game', 'Shots/Game', 'Shooting %'].forEach((col, i) => {
                    const dataKey = ['Player', 'Team', 'GP', 'GPG', 'APG', 'PTSPG', 'ShotsPG', 'SH%'][i];
                    const sortClass = sortConfig.key === dataKey ? (sortConfig.direction === 'asc' ? 'sorted-asc' : 'sorted-desc') : '';
                    html += `<th class="sortable ${sortClass} ${col !== 'Player' && col !== 'Team' ? 'text-center' : ''}" onclick="sortConfig.key='${dataKey}'; renderCurrentView()">${col}</th>`;
                });
                html += '</tr></thead><tbody>';
                sorted.forEach(p => {
                    const bgColor = getTeamColor(p.Team);
                    const gpg = p.GP > 0 ? (p.G / p.GP).toFixed(2) : '0.00';
                    const apg = p.GP > 0 ? (p.A / p.GP).toFixed(2) : '0.00';
                    const ptspg = p.GP > 0 ? (p.PTS / p.GP).toFixed(2) : '0.00';
                    const shotspg = p.GP > 0 ? (p.Shots / p.GP).toFixed(1) : '0.0';
                    const sh = p.Shots > 0 ? ((p.G / p.Shots) * 100).toFixed(1) : '0.0';
                    
                    p.GPG = parseFloat(gpg);
                    p.APG = parseFloat(apg);
                    p.PTSPG = parseFloat(ptspg);
                    p.ShotsPG = parseFloat(shotspg);
                    p['SH%'] = parseFloat(sh);
                    
                    html += `<tr style="background-color: ${bgColor}">
                        <td>${p.Player}</td>
                        <td>${p.Team}</td>
                        <td class="text-center">${p.GP}</td>
                        <td class="text-center">${gpg}</td>
                        <td class="text-center">${apg}</td>
                        <td class="text-center highlight">${ptspg}</td>
                        <td class="text-center">${shotspg}</td>
                        <td class="text-center">${sh}%</td>
                    </tr>`;
                });
            } else {
                ['Player', 'Team', 'Games', 'Goals', 'Assists', 'Points', 'Shots', 'Passes', 'Hits', 'Interceptions', 'Blocks'].forEach((col, i) => {
                    const dataKey = ['Player', 'Team', 'GP', 'G', 'A', 'PTS', 'Shots', 'Passes', 'Hits', 'Int', 'Blk'][i];
                    const sortClass = sortConfig.key === dataKey ? (sortConfig.direction === 'asc' ? 'sorted-asc' : 'sorted-desc') : '';
                    html += `<th class="sortable ${sortClass} ${col !== 'Player' && col !== 'Team' ? 'text-center' : ''}" onclick="sortConfig.key='${dataKey}'; renderCurrentView()">${col}</th>`;
                });
                html += '</tr></thead><tbody>';
                sorted.forEach(p => {
                    const bgColor = getTeamColor(p.Team);
                    html += `<tr style="background-color: ${bgColor}">
                        <td>${p.Player}</td>
                        <td>${p.Team}</td>
                        <td class="text-center">${p.GP}</td>
                        <td class="text-center">${p.G}</td>
                        <td class="text-center">${p.A}</td>
                        <td class="text-center highlight">${p.PTS}</td>
                        <td class="text-center">${p.Shots}</td>
                        <td class="text-center">${p.Passes}</td>
                        <td class="text-center">${p.Hits}</td>
                        <td class="text-center">${p.Int}</td>
                        <td class="text-center">${p.Blk}</td>
                    </tr>`;
                });
            }
            html += '</tbody></table></div>';
            document.getElementById('content').innerHTML = html;
        }
        
        function renderGoalieStats() {
            if (allData.goalies.length === 0) {
                document.getElementById('content').innerHTML = '<div class="loading">No goalie data</div>';
                return;
            }
            const sorted = [...allData.goalies];
            sortTable(sortConfig.key, sorted);
            
            let html = '<div class="toggle-container">';
            html += '<span class="toggle-label">View:</span>';
            html += `<button class="toggle-button ${!showPerGame ? 'active' : ''}" onclick="showPerGame = false; renderCurrentView()">Totals</button>`;
            html += `<button class="toggle-button ${showPerGame ? 'active' : ''}" onclick="showPerGame = true; renderCurrentView()">Per Game</button>`;
            html += '</div>';
            
            html += '<div class="table-container"><table><thead><tr>';
            
            if (showPerGame) {
                ['Goalie', 'Team', 'Games', 'Wins', 'Losses', 'OT Wins', 'OT Losses', 'Goals Against/Game', 'Shots Against/Game', 'Saves/Game', 'Save %', 'Goals Against Avg'].forEach((col, i) => {
                    const dataKey = ['Goalie', 'Team', 'GP', 'W', 'L', 'OTW', 'OTL', 'GAPG', 'SAPG', 'SVPG', 'SV%', 'GAA'][i];
                    const sortClass = sortConfig.key === dataKey ? (sortConfig.direction === 'asc' ? 'sorted-asc' : 'sorted-desc') : '';
                    html += `<th class="sortable ${sortClass} ${col !== 'Goalie' && col !== 'Team' ? 'text-center' : ''}" onclick="sortConfig.key='${dataKey}'; renderCurrentView()">${col}</th>`;
                });
                html += '</tr></thead><tbody>';
                sorted.forEach(g => {
                    const bgColor = getTeamColor(g.Team);
                    const gapg = g.GP > 0 ? (g.GA / g.GP).toFixed(2) : '0.00';
                    const sapg = g.GP > 0 ? (g.SA / g.GP).toFixed(1) : '0.0';
                    const svpg = g.GP > 0 ? (g.SV / g.GP).toFixed(1) : '0.0';
                    
                    g.GAPG = parseFloat(gapg);
                    g.SAPG = parseFloat(sapg);
                    g.SVPG = parseFloat(svpg);
                    
                    html += `<tr style="background-color: ${bgColor}">
                        <td>${g.Goalie}</td>
                        <td>${g.Team}</td>
                        <td class="text-center">${g.GP}</td>
                        <td class="text-center record-good">${g.W}</td>
                        <td class="text-center record-bad">${g.L}</td>
                        <td class="text-center" style="color: #3b82f6; font-weight: 700;">${g.OTW}</td>
                        <td class="text-center" style="color: #f59e0b; font-weight: 700;">${g.OTL}</td>
                        <td class="text-center">${gapg}</td>
                        <td class="text-center">${sapg}</td>
                        <td class="text-center">${svpg}</td>
                        <td class="text-center highlight">${g['SV%']}%</td>
                        <td class="text-center highlight">${g.GAA}</td>
                    </tr>`;
                });
            } else {
                ['Goalie', 'Team', 'Games', 'Wins', 'Losses', 'OT Wins', 'OT Losses', 'Goals Against', 'Shots Against', 'Saves', 'Save %', 'Goals Against Avg'].forEach((col, i) => {
                    const dataKey = ['Goalie', 'Team', 'GP', 'W', 'L', 'OTW', 'OTL', 'GA', 'SA', 'SV', 'SV%', 'GAA'][i];
                    const sortClass = sortConfig.key === dataKey ? (sortConfig.direction === 'asc' ? 'sorted-asc' : 'sorted-desc') : '';
                    html += `<th class="sortable ${sortClass} ${col !== 'Goalie' && col !== 'Team' ? 'text-center' : ''}" onclick="sortConfig.key='${dataKey}'; renderCurrentView()">${col}</th>`;
                });
                html += '</tr></thead><tbody>';
                sorted.forEach(g => {
                    const bgColor = getTeamColor(g.Team);
                    html += `<tr style="background-color: ${bgColor}">
                        <td>${g.Goalie}</td>
                        <td>${g.Team}</td>
                        <td class="text-center">${g.GP}</td>
                        <td class="text-center record-good">${g.W}</td>
                        <td class="text-center record-bad">${g.L}</td>
                        <td class="text-center" style="color: #3b82f6; font-weight: 700;">${g.OTW}</td>
                        <td class="text-center" style="color: #f59e0b; font-weight: 700;">${g.OTL}</td>
                        <td class="text-center">${g.GA}</td>
                        <td class="text-center">${g.SA}</td>
                        <td class="text-center">${g.SV}</td>
                        <td class="text-center highlight">${g['SV%']}%</td>
                        <td class="text-center highlight">${g.GAA}</td>
                    </tr>`;
                });
            }
            html += '</tbody></table></div>';
            document.getElementById('content').innerHTML = html;
        }
        
        function renderTeamStats() {
            let teamArray = Object.values(allData.teams);
            if (teamArray.length === 0) {
                document.getElementById('content').innerHTML = '<div class="loading">No team data</div>';
                return;
            }
            
            teamArray.forEach(t => {
                t.Diff = t.GF - t.GA;
            });
            
            sortTable(sortConfig.key, teamArray);
            
            let html = '<div class="toggle-container">';
            html += '<span class="toggle-label">View:</span>';
            html += `<button class="toggle-button ${!showPerGame ? 'active' : ''}" onclick="showPerGame = false; renderCurrentView()">Totals</button>`;
            html += `<button class="toggle-button ${showPerGame ? 'active' : ''}" onclick="showPerGame = true; renderCurrentView()">Per Game</button>`;
            html += '</div>';
            
            html += '<div class="table-container"><table><thead><tr>';
            
            if (showPerGame) {
                ['Team', 'Games', 'Goals For/Game', 'Goals Against/Game', 'Diff/Game', 'Shooting %', 'Pass %', 'Time on Attack', 'Faceoffs/Game'].forEach((col, i) => {
                    const dataKey = ['Team', 'GP', 'GFPG', 'GAPG', 'DiffPG', 'TeamSH%', 'PassPct', 'TimeOnAttack', 'FOPG'][i];
                    const sortClass = sortConfig.key === dataKey ? (sortConfig.direction === 'asc' ? 'sorted-asc' : 'sorted-desc') : '';
                    html += `<th class="sortable ${sortClass} ${col !== 'Team' ? 'text-center' : ''}" onclick="sortConfig.key='${dataKey}'; renderCurrentView()">${col}</th>`;
                });
                html += '</tr></thead><tbody>';
                teamArray.forEach(t => {
                    const bgColor = getTeamColor(t.Team);
                    const gfpg = t.GP > 0 ? (t.GF / t.GP).toFixed(2) : '0.00';
                    const gapg = t.GP > 0 ? (t.GA / t.GP).toFixed(2) : '0.00';
                    const diffpg = t.GP > 0 ? (t.Diff / t.GP).toFixed(2) : '0.00';
                    const teamSh = t.Shots > 0 ? ((t.G / t.Shots) * 100).toFixed(1) : '0.0';
                    const fopg = t.GP > 0 ? (t.FaceoffWon / t.GP).toFixed(1) : '0.0';
                    
                    t.GFPG = parseFloat(gfpg);
                    t.GAPG = parseFloat(gapg);
                    t.DiffPG = parseFloat(diffpg);
                    t['TeamSH%'] = parseFloat(teamSh);
                    t.FOPG = parseFloat(fopg);
                    
                    html += `<tr style="background-color: ${bgColor}">
                        <td class="team-name">${t.Team}</td>
                        <td class="text-center">${t.GP}</td>
                        <td class="text-center highlight">${gfpg}</td>
                        <td class="text-center">${gapg}</td>
                        <td class="text-center">${diffpg > 0 ? '+' : ''}${diffpg}</td>
                        <td class="text-center">${teamSh}%</td>
                        <td class="text-center">${t.PassPct}%</td>
                        <td class="text-center">${t.TimeOnAttack}</td>
                        <td class="text-center">${fopg}</td>
                    </tr>`;
                });
            } else {
                ['Team', 'Games', 'Wins', 'Losses', 'OT Wins', 'OT Losses', 'Points', 'Reg Wins', 'Goals For', 'Goals Against', 'Diff', 'Shots', 'Passes', 'Pass%', 'Hits', 'Interceptions', 'Blocks', 'Time on Attack', 'Faceoffs Won'].forEach((col, i) => {
                    const dataKey = ['Team', 'GP', 'W', 'L', 'OTW', 'OTL', 'PTS', 'RW', 'GF', 'GA', 'Diff', 'Shots', 'Passes', 'PassPct', 'Hits', 'Int', 'Blk', 'TimeOnAttack', 'FaceoffWon'][i];
                    const sortClass = sortConfig.key === dataKey ? (sortConfig.direction === 'asc' ? 'sorted-asc' : 'sorted-desc') : '';
                    html += `<th class="sortable ${sortClass} ${col !== 'Team' ? 'text-center' : ''}" onclick="sortConfig.key='${dataKey}'; renderCurrentView()">${col}</th>`;
                });
                html += '</tr></thead><tbody>';
                teamArray.forEach(t => {
                    const diff = t.GF - t.GA;
                    const bgColor = getTeamColor(t.Team);
                    html += `<tr style="background-color: ${bgColor}">
                        <td class="team-name">${t.Team}</td>
                        <td class="text-center">${t.GP}</td>
                        <td class="text-center record-good">${t.W}</td>
                        <td class="text-center record-bad">${t.L}</td>
                        <td class="text-center" style="color: #3b82f6; font-weight: 700;">${t.OTW}</td>
                        <td class="text-center" style="color: #f59e0b; font-weight: 700;">${t.OTL}</td>
                        <td class="text-center highlight">${t.PTS}</td>
                        <td class="text-center">${t.RW}</td>
                        <td class="text-center">${t.GF}</td>
                        <td class="text-center">${t.GA}</td>
                        <td class="text-center">${diff > 0 ? '+' : ''}${diff}</td>
                        <td class="text-center">${t.Shots}</td>
                        <td class="text-center">${t.Passes}</td>
                        <td class="text-center">${t.PassPct}%</td>
                        <td class="text-center">${t.Hits}</td>
                        <td class="text-center">${t.Int}</td>
                        <td class="text-center">${t.Blk}</td>
                        <td class="text-center">${t.TimeOnAttack}</td>
                        <td class="text-center">${t.FaceoffWon}</td>
                    </tr>`;
                });
            }
            html += '</tbody></table></div>';
            document.getElementById('content').innerHTML = html;
        }
        
        function renderStandings() {
            let teamArray = Object.values(allData.teams);
            if (teamArray.length === 0) {
                document.getElementById('content').innerHTML = '<div class="loading">No data</div>';
                return;
            }
            
            teamArray.sort((a, b) => {
                if (b.PTS !== a.PTS) return b.PTS - a.PTS;
                if (b.RW !== a.RW) return b.RW - a.RW;
                return (b.GF - b.GA) - (a.GF - a.GA);
            });
            
            let html = '<div class="table-container"><table><thead><tr>';
            ['Rank', 'Team', 'Games', 'Reg Wins', 'Reg Losses', 'OT Wins', 'OT Losses', 'Points'].forEach(col => {
                html += `<th class="${col !== 'Team' ? 'text-center' : ''}">${col}</th>`;
            });
            html += '</tr></thead><tbody>';
            teamArray.forEach((t, i) => {
                const rank = i + 1;
                const rankDisplay = i === 0 ? '🥇 1' : i === 1 ? '🥈 2' : i === 2 ? '🥉 3' : rank;
                const bgColor = getTeamColor(t.Team);
                html += `<tr style="background-color: ${bgColor}"><td class="text-center">${rankDisplay}</td><td class="team-name">${t.Team}</td><td class="text-center">${t.GP}</td><td class="text-center record-good">${t.RW}</td><td class="text-center record-bad">${t.L}</td><td class="text-center" style="color: #3b82f6; font-weight: 700;">${t.OTW}</td><td class="text-center" style="color: #f59e0b; font-weight: 700;">${t.OTL}</td><td class="text-center highlight">${t.PTS}</td></tr>`;
            });
            html += '</tbody></table></div>';
            document.getElementById('content').innerHTML = html;
        }
        
        function renderSchedule() {
            const completedGames = {};
            if (allData.rawTeamData.length > 0) {
                allData.rawTeamData.forEach(row => {
                    const game = parseInt(row.Game || row.GAME) || 0;
                    let team = String(row.Team || row.TEAM || '').trim();
                    if (team === 'Les Cities D\'or') team = 'Les Cities D\'Or';
                    if (team === 'Wildlings') team = 'The Wildlings';
                    const score = parseInt(row.Score || row.SCORE) || 0;
                    const winType = String(row.WinType || row.WINTYPE || 'REG').trim().toUpperCase();
                    
                    if (!completedGames[game]) completedGames[game] = {};
                    completedGames[game][team] = { score, winType };
                });
            }
            
            let html = '<div class="info-banner">📅 Season 4 Regular Season Schedule — 45 Games · 10 Teams · True Round-Robin</div>';
            
            let currentWeek = 0;
            SCHEDULE.forEach(g => {
                if (g.week !== currentWeek) {
                    if (currentWeek !== 0) html += '</tbody></table></div>';
                    currentWeek = g.week;
                    html += `<div style="margin-bottom: 10px; margin-top: 20px; font-family: 'Teko', sans-serif; font-size: 1.8rem; font-weight: 700; color: #01b8bf; text-transform: uppercase;">Week ${g.week}</div>`;
                    html += '<div class="table-container"><table><thead><tr><th class="text-center">Game</th><th class="text-center">Day</th><th>Home</th><th class="text-center">Score</th><th>Away</th><th class="text-center">Status</th></tr></thead><tbody>';
                }
                
                const homeColor = getTeamColor(g.home);
                const awayColor = getTeamColor(g.away);
                
                // Try both normalized and raw team name lookups
                const homeKey = g.home;
                const awayKey = g.away;
                const gameData = completedGames[g.game] || {};
                const homeData = gameData[homeKey];
                const awayData = gameData[awayKey];
                const isComplete = homeData && awayData;
                
                let scoreDisplay = '-';
                let status = '⏳ Pending';
                let rowStyle = '';
                
                if (isComplete) {
                    const homeWon = homeData.score > awayData.score;
                    const winType = homeWon ? homeData.winType : awayData.winType;
                    const overtimeLabel = (winType === 'OT' || winType === 'SO') ? ' (OT)' : '';
                    
                    scoreDisplay = `<span style="font-weight: ${homeWon ? '700' : '500'}; color: ${homeWon ? '#10b981' : '#666'}">${homeData.score}</span> - <span style="font-weight: ${!homeWon ? '700' : '500'}; color: ${!homeWon ? '#10b981' : '#666'}">${awayData.score}</span>${overtimeLabel}`;
                    status = '✅ Final';
                    rowStyle = 'style="background: rgba(1, 184, 191, 0.05)"';
                }
                
                html += `<tr ${rowStyle}>
                    <td class="text-center" style="font-weight: 700">${g.game}</td>
                    <td class="text-center">${g.day}</td>
                    <td style="background-color: ${homeColor}"><span class="team-name">${g.home}</span></td>
                    <td class="text-center" style="font-weight: 700">${scoreDisplay}</td>
                    <td style="background-color: ${awayColor}"><span class="team-name">${g.away}</span></td>
                    <td class="text-center">${status}</td>
                </tr>`;
            });
            
            html += '</tbody></table></div>';
            document.getElementById('content').innerHTML = html;
        }
        
        function renderHeadToHead() {
            const h2hRecords = {};
            const allTeams = Object.keys(allData.teams).sort();
            
            allTeams.forEach(team => {
                h2hRecords[team] = {};
                allTeams.forEach(opp => {
                    h2hRecords[team][opp] = { w: 0, l: 0, otl: 0 };
                });
            });
            
            const completedGames = {};
            if (allData.rawTeamData.length > 0) {
                allData.rawTeamData.forEach(row => {
                    const game = parseInt(row.Game || row.GAME) || 0;
                    let team = String(row.Team || row.TEAM || '').trim();
                    if (team === 'Les Cities D\'or') team = 'Les Cities D\'Or';
                    if (team === 'Wildlings') team = 'The Wildlings';
                    const score = parseInt(row.Score || row.SCORE) || 0;
                    const winType = String(row.WinType || row.WINTYPE || 'REG').trim().toUpperCase();
                    
                    if (!completedGames[game]) completedGames[game] = {};
                    completedGames[game][team] = { score, winType };
                });
            }
            
            SCHEDULE.forEach(g => {
                const gameData = completedGames[g.game];
                if (gameData) {
                    const homeData = gameData[g.home];
                    const awayData = gameData[g.away];
                    
                    if (homeData && awayData) {
                        if (homeData.score > awayData.score) {
                            if (homeData.winType === 'OT' || homeData.winType === 'SO') {
                                if (h2hRecords[g.home] && h2hRecords[g.home][g.away]) h2hRecords[g.home][g.away].w += 1;
                                if (h2hRecords[g.away] && h2hRecords[g.away][g.home]) h2hRecords[g.away][g.home].otl += 1;
                            } else {
                                if (h2hRecords[g.home] && h2hRecords[g.home][g.away]) h2hRecords[g.home][g.away].w += 1;
                                if (h2hRecords[g.away] && h2hRecords[g.away][g.home]) h2hRecords[g.away][g.home].l += 1;
                            }
                        } else {
                            if (awayData.winType === 'OT' || awayData.winType === 'SO') {
                                if (h2hRecords[g.away] && h2hRecords[g.away][g.home]) h2hRecords[g.away][g.home].w += 1;
                                if (h2hRecords[g.home] && h2hRecords[g.home][g.away]) h2hRecords[g.home][g.away].otl += 1;
                            } else {
                                if (h2hRecords[g.away] && h2hRecords[g.away][g.home]) h2hRecords[g.away][g.home].w += 1;
                                if (h2hRecords[g.home] && h2hRecords[g.home][g.away]) h2hRecords[g.home][g.away].l += 1;
                            }
                        }
                    }
                }
            });
            
            let html = '<div class="info-banner">⚔️ Head-to-Head Records — Each pair plays once</div>';
            html += '<div class="table-container"><table class="h2h-matrix"><thead><tr>';
            html += '<th>Team</th>';
            allTeams.forEach(team => {
                const shortName = team.split(' ').map(w => w[0]).join('');
                html += `<th class="text-center" title="${team}">${shortName}</th>`;
            });
            html += '</tr></thead><tbody>';
            
            allTeams.forEach(team => {
                const bgColor = getTeamColor(team);
                html += `<tr><td class="team-name" style="background-color: ${bgColor}">${team}</td>`;
                
                allTeams.forEach(opp => {
                    if (team === opp) {
                        html += '<td class="text-center h2h-none">-</td>';
                    } else {
                        const record = h2hRecords[team][opp];
                        const total = record.w + record.l + record.otl;
                        
                        if (total === 0) {
                            html += '<td class="text-center h2h-none">-</td>';
                        } else {
                            let cellClass = 'h2h-tie';
                            if (record.w > record.l + record.otl) cellClass = 'h2h-win';
                            else if (record.w < record.l + record.otl) cellClass = 'h2h-loss';
                            
                            const display = record.otl > 0 
                                ? `${record.w}-${record.l}-${record.otl}` 
                                : `${record.w}-${record.l}`;
                            html += `<td class="text-center ${cellClass}">${display}</td>`;
                        }
                    }
                });
                html += '</tr>';
            });
            
            html += '</tbody></table></div>';
            html += '<div class="info-banner" style="margin-top: 20px;">Record format: Wins-Losses-OT Losses</div>';
            document.getElementById('content').innerHTML = html;
        }
        
        fetchData();
    </script>
</body>
</html>
