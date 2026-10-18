<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>百灵鸟播放器 - LARK ART STUDIO</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; }
        body { background-color: #090d16; color: #f1f5f9; min-height: 100vh; display: flex; flex-direction: column; user-select: none; }
        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: rgba(15, 23, 42, 0.6); }
        ::-webkit-scrollbar-thumb { background: rgba(56, 189, 248, 0.3); border-radius: 3px; }
        .glass { background: rgba(30, 41, 59, 0.75); backdrop-filter: blur(16px); -webkit-backdrop-filter: blur(16px); border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 16px; }
        header { display: flex; justify-content: space-between; align-items: center; padding: 16px 24px; border-bottom: 1px solid rgba(255,255,255,0.08); position: sticky; top: 0; z-index: 50; background: rgba(9, 13, 22, 0.85); backdrop-filter: blur(12px); }
        .logo-area { display: flex; align-items: center; gap: 12px; }
        .logo-icon { width: 40px; height: 40px; background: linear-gradient(135deg, #0ea5e9, #6366f1); border-radius: 12px; display: flex; align-items: center; justify-content: center; box-shadow: 0 4px 12px rgba(14, 165, 233, 0.3); color: white; font-size: 18px; }
        .logo-text h1 { font-size: 18px; font-weight: 700; background: linear-gradient(90deg, #38bdf8, #818cf8); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .logo-text p { font-size: 11px; color: #94a3b8; letter-spacing: 0.5px; }
        .role-bar { display: flex; align-items: center; gap: 12px; }
        .role-badge { display: flex; align-items: center; gap: 8px; padding: 6px 14px; border-radius: 20px; font-size: 12px; background: rgba(30, 41, 59, 0.9); border: 1px solid rgba(255,255,255,0.1); }
        .role-dot { width: 8px; height: 8px; border-radius: 50%; }
        .role-dot.user { background-color: #f59e0b; }
        .role-dot.admin { background-color: #10b981; box-shadow: 0 0 8px #10b981; }
        .btn { padding: 8px 16px; border-radius: 12px; font-size: 12px; font-weight: 600; cursor: pointer; border: none; transition: all 0.2s; display: inline-flex; align-items: center; gap: 6px; }
        .btn-primary { background-color: #0284c7; color: white; box-shadow: 0 4px 12px rgba(2, 132, 199, 0.3); }
        .btn-success { background-color: #059669; color: white; }
        .btn-secondary { background-color: #334155; color: #cbd5e1; }
        main { max-width: 1280px; width: 100%; margin: 24px auto; padding: 0 20px; display: grid; grid-template-columns: 350px 1fr; gap: 24px; flex: 1; }
        @media(max-width: 900px) { main { grid-template-columns: 1fr; } }
        .playlist-panel { display: flex; flex-direction: column; height: calc(100vh - 130px); padding: 20px; }
        .panel-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 16px; padding-bottom: 12px; border-bottom: 1px solid rgba(255,255,255,0.08); font-size: 14px; font-weight: 600; color: #38bdf8; }
        .playlist-container { flex: 1; overflow-y: auto; display: flex; flex-direction: column; gap: 8px; padding-right: 4px; }
        .playlist-item { display: flex; align-items: center; justify-content: space-between; padding: 10px 12px; border-radius: 12px; background: rgba(15, 23, 42, 0.6); border: 1px solid rgba(255,255,255,0.04); cursor: pointer; transition: all 0.2s; gap: 10px; }
        .playlist-item:hover { background: rgba(30, 41, 59, 0.9); border-color: rgba(56, 189, 248, 0.3); }
        .playlist-item.active { background: rgba(14, 165, 233, 0.15); border-color: rgba(14, 165, 233, 0.5); color: #38bdf8; }
        .playlist-item img { width: 36px; height: 36px; border-radius: 8px; object-fit: cover; flex-shrink: 0; }
        .playlist-info { flex: 1; overflow: hidden; }
        .playlist-info h4 { font-size: 12px; font-weight: 600; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .playlist-info p { font-size: 10px; color: #94a3b8; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; margin-top: 2px; }
        .item-actions { display: flex; gap: 4px; flex-shrink: 0; }
        .icon-btn { width: 28px; height: 28px; border-radius: 8px; background: #1e293b; border: none; color: #cbd5e1; display: flex; align-items: center; justify-content: center; cursor: pointer; font-size: 11px; transition: 0.2s; }
        .icon-btn:hover { background: #334155; color: white; }
        .icon-btn.edit:hover { background: #d97706; }
        .icon-btn.delete:hover { background: #e11d48; }
        .player-right { display: flex; flex-direction: column; gap: 20px; }
        .viewport-card { padding: 24px; position: relative; overflow: hidden; display: flex; flex-direction: column; align-items: center; justify-content: center; min-height: 360px; }
        .bg-blur { position: absolute; inset: 0; background-size: cover; background-position: center; filter: blur(40px); opacity: 0.15; pointer-events: none; z-index: 0; }
        .media-wrapper { position: relative; z-index: 1; width: 100%; max-height: 300px; display: flex; align-items: center; justify-content: center; border-radius: 12px; overflow: hidden; background: #020617; box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.5); }
        video { width: 100%; max-height: 300px; object-fit: contain; }
        .audio-view { position: relative; width: 100%; height: 280px; display: flex; flex-direction: column; align-items: center; justify-content: center; }
        canvas { position: absolute; inset: 0; width: 100%; height: 100%; pointer-events: none; opacity: 0.5; }
        .cover-art { position: relative; z-index: 2; width: 140px; height: 140px; border-radius: 20px; overflow: hidden; box-shadow: 0 10px 25px rgba(0,0,0,0.5); border: 2px solid rgba(255,255,255,0.1); }
        .cover-art img { width: 100%; height: 100%; object-fit: cover; }
        .track-meta { position: relative; z-index: 2; text-align: center; margin-top: 14px; }
        .track-meta h3 { font-size: 16px; font-weight: 700; color: #f8fafc; max-width: 400px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .track-meta p { font-size: 12px; color: #38bdf8; margin-top: 4px; }
        .controls-row { width: 100%; z-index: 1; margin-top: 20px; display: flex; flex-direction: column; gap: 12px; }
        .progress-box { width: 100%; }
        .time-labels { display: flex; justify-content: space-between; font-size: 11px; color: #94a3b8; margin-bottom: 4px; }
        input[type="range"] { width: 100%; height: 4px; background: #334155; border-radius: 2px; appearance: none; cursor: pointer; accent-color: #38bdf8; }
        .main-controls { display: flex; justify-content: space-between; align-items: center; width: 100%; margin-top: 4px; }
        .left-tools, .right-tools { display: flex; align-items: center; gap: 12px; }
        .center-buttons { display: flex; align-items: center; gap: 16px; }
        .ctrl-btn { background: #1e293b; border: 1px solid rgba(255,255,255,0.08); color: #cbd5e1; width: 36px; height: 36px; border-radius: 50%; display: flex; align-items: center; justify-content: center; cursor: pointer; transition: 0.2s; font-size: 13px; }
        .ctrl-btn:hover { background: #334155; color: white; border-color: #38bdf8; }
        .ctrl-btn.play-lg { width: 50px; height: 50px; background: linear-gradient(135deg, #0ea5e9, #6366f1); color: white; font-size: 18px; border: none; box-shadow: 0 4px 15px rgba(14, 165, 233, 0.4); }
        .ctrl-btn.play-lg:hover { transform: scale(1.05); }
        select { background: #1e293b; color: #cbd5e1; border: 1px solid rgba(255,255,255,0.08); padding: 4px 8px; border-radius: 8px; font-size: 11px; outline: none; }
        .lyrics-card { padding: 20px; height: 170px; display: flex; flex-direction: column; }
        .lyrics-box { flex: 1; overflow-y: auto; text-align: center; display: flex; flex-direction: column; gap: 10px; padding: 4px 0; scroll-behavior: smooth; }
        .lyric-line { font-size: 12px; color: #94a3b8; transition: all 0.3s; cursor: pointer; }
        .lyric-line:hover { color: #f8fafc; }
        .lyric-line.active { color: #38bdf8; font-size: 14px; font-weight: 700; text-shadow: 0 0 10px rgba(56, 189, 248, 0.4); }
        .modal-overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.8); backdrop-filter: blur(8px); display: none; align-items: center; justify-content: center; z-index: 100; padding: 20px; }
        .modal-box { width: 100%; max-width: 480px; padding: 24px; background: #0f172a; border: 1px solid rgba(255,255,255,0.12); border-radius: 20px; box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.7); }
        .modal-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 16px; padding-bottom: 10px; border-bottom: 1px solid rgba(255,255,255,0.08); font-size: 15px; font-weight: 700; color: #38bdf8; }
        .form-group { margin-bottom: 14px; }
        .form-group label { display: block; font-size: 11px; color: #94a3b8; margin-bottom: 6px; }
        .form-control { width: 100%; background: #1e293b; border: 1px solid rgba(255,255,255,0.08); border-radius: 10px; padding: 8px 12px; font-size: 12px; color: #f8fafc; outline: none; }
        .form-control:focus { border-color: #38bdf8; }
        textarea.form-control { resize: vertical; font-family: monospace; }
        .modal-footer { display: flex; justify-content: flex-end; gap: 10px; margin-top: 20px; }
        .hidden { display: none !important; }
    </style>
</head>
<body>
    <header>
        <div class="logo-area">
            <div class="logo-icon"><i class="fa-solid fa-dove"></i></div>
            <div class="logo-text">
                <h1>百灵鸟播放器</h1>
                <p>LARK ART STUDIO · 专属视听工作室</p>
            </div>
        </div>
        <div class="role-bar">
            <div class="role-badge">
                <span id="roleDot" class="role-dot user"></span>
                <span id="roleText">普通用户模式</span>
            </div>
            <button onclick="toggleRole()" class="btn btn-primary">
                <i class="fa-solid fa-user-shield"></i>
                <span id="roleBtnText">切换为管理员</span>
            </button>
        </div>
    </header>

    <main>
        <section class="glass playlist-panel">
            <div class="panel-header">
                <span><i class="fa-solid fa-list-music"></i> 视听曲库</span>
                <button id="uploadBtn" onclick="openModal()" class="btn btn-success hidden" style="padding: 4px 10px; font-size: 11px;">
                    <i class="fa-solid fa-cloud-arrow-up"></i> 上传曲目
                </button>
            </div>
            <div id="playlistContainer" class="playlist-container"></div>
        </section>

        <div class="player-right">
            <section class="glass viewport-card">
                <div id="bgCoverBlur" class="bg-blur"></div>
                <div class="media-wrapper">
                    <video id="videoElement" class="hidden" playsinline crossorigin="anonymous"></video>
                    <div id="audioView" class="audio-view">
                        <canvas id="visualizerCanvas"></canvas>
                        <div class="cover-art">
                            <img id="currentCover" src="" alt="Cover">
                        </div>
                        <div class="track-meta">
                            <h3 id="currentTitle">请选择曲目</h3>
                            <p id="currentArtist">百灵鸟艺术工作室</p>
                        </div>
                    </div>
                </div>

                <div style="width: 100%; display: flex; justify-content: space-between; align-items: center; margin-top: 14px; z-index: 1; font-size: 11px; color: #94a3b8;">
                    <div style="display: flex; gap: 6px; align-items: center;">
                        <span>特效:</span>
                        <button onclick="setVisMode('bars')" class="vis-btn btn btn-secondary" style="padding: 3px 8px; font-size: 10px; color: #38bdf8;">频谱柱</button>
                        <button onclick="setVisMode('wave')" class="vis-btn btn btn-secondary" style="padding: 3px 8px; font-size: 10px;">波形图</button>
                        <button onclick="setVisMode('circle')" class="vis-btn btn btn-secondary" style="padding: 3px 8px; font-size: 10px;">圆环谱</button>
                    </div>
                    <div style="display: flex; gap: 6px;">
                        <button id="pipBtn" onclick="togglePiP()" class="btn btn-secondary hidden" style="padding: 3px 8px; font-size: 10px;"><i class="fa-solid fa-clone"></i> 画中画</button>
                        <button onclick="toggleFullscreen()" class="btn btn-secondary" style="padding: 3px 8px; font-size: 10px;"><i class="fa-solid fa-expand"></i> 全屏</button>
                    </div>
                </div>

                <div class="controls-row">
                    <div class="progress-box">
                        <div class="time-labels">
                            <span id="currentTime">00:00</span>
                            <span id="duration">00:00</span>
                        </div>
                        <input type="range" id="progressBar" value="0" min="0" max="100" step="0.1">
                    </div>

                    <div class="main-controls">
                        <div class="left-tools">
                            <button id="shuffleBtn" onclick="toggleShuffle()" class="ctrl-btn" title="随机播放"><i class="fa-solid fa-shuffle"></i></button>
                            <button id="loopBtn" onclick="toggleLoop()" class="ctrl-btn" title="单曲循环"><i class="fa-solid fa-repeat"></i></button>
                        </div>
                        <div class="center-buttons">
                            <button onclick="prevTrack()" class="ctrl-btn" title="上一首"><i class="fa-solid fa-backward-step"></i></button>
                            <button onclick="togglePlay()" id="playPauseBtn" class="ctrl-btn play-lg" title="播放/暂停"><i class="fa-solid fa-play" id="playPauseIcon"></i></button>
                            <button onclick="nextTrack()" class="ctrl-btn" title="下一首"><i class="fa-solid fa-forward-step"></i></button>
                        </div>
                        <div class="right-tools">
                            <button onclick="toggleMute()" id="muteBtn" class="ctrl-btn"><i class="fa-solid fa-volume-high"></i></button>
                            <input type="range" id="volumeBar" min="0" max="1" step="0.01" value="1" style="width: 70px;">
                            <select id="speedSelect" onchange="changeSpeed()">
                                <option value="0.5">0.5x</option>
                                <option value="0.75">0.75x</option>
                                <option value="1.0" selected>1.0x</option>
                                <option value="1.25">1.25x</option>
                                <option value="1.5">1.5x</option>
                                <option value="2.0">2.0x</option>
                            </select>
                        </div>
                    </div>
                </div>
            </section>

            <section class="glass lyrics-card">
                <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 8px; padding-bottom: 6px; border-bottom: 1px solid rgba(255,255,255,0.06); font-size: 12px; font-weight: 600;">
                    <span><i class="fa-solid fa-microphone-lines" style="color: #38bdf8;"></i> 同步歌词 (LRC)</span>
                    <span id="lyricStatus" style="font-size: 10px; color: #94a3b8;">暂无歌词</span>
                </div>
                <div id="lyricsContainer" class="lyrics-box">
                    <p style="color: #64748b; font-size: 12px; margin-top: 20px;">请选择曲目查看同步歌词</p>
                </div>
            </section>
        </div>
    </main>

    <div id="modalOverlay" class="modal-overlay">
        <div class="modal-box">
            <div class="modal-header">
                <span id="modalTitle">管理员上传曲目</span>
                <button onclick="closeModal()" style="background:none; border:none; color:#94a3b8; cursor:pointer; font-size:16px;"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <form id="mediaForm" onsubmit="handleFormSubmit(event)">
                <input type="hidden" id="editIndex" value="-1">
                <div class="form-group">
                    <label>歌曲 / 视频名称 *</label>
                    <input type="text" id="inputTitle" required class="form-control" placeholder="例如：水乡谣 / 琬婷之歌">
                </div>
                <div class="form-group">
                    <label>艺术家 / 创作者 *</label>
                    <input type="text" id="inputArtist" required class="form-control" placeholder="例如：琬婷 / 百灵鸟艺术工作室">
                </div>
                <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 12px;" class="form-group">
                    <div>
                        <label>媒体文件 (音频/视频) *</label>
                        <input type="file" id="inputFile" accept="audio/*,video/*" style="font-size: 10px; color: #94a3b8;">
                    </div>
                    <div>
                        <label>封面图片 (可选)</label>
                        <input type="file" id="inputCover" accept="image/*" style="font-size: 10px; color: #94a3b8;">
                    </div>
                </div>
                <div class="form-group">
                    <label>LRC 同步歌词 (可选，支持直接粘贴)</label>
                    <textarea id="inputLyrics" rows="4" class="form-control" placeholder="[00:12.34] 第一句歌词&#10;[00:15.67] 第二句歌词"></textarea>
                </div>
                <div class="modal-footer">
                    <button type="button" onclick="closeModal()" class="btn btn-secondary">取消</button>
                    <button type="submit" class="btn btn-primary">确认保存</button>
                </div>
            </form>
        </div>
    </div>

    <script>
        let playlist = JSON.parse(localStorage.getItem('lark_playlist')) || [
            {
                title: "水乡谣",
                artist: "琬婷 / 百灵鸟艺术工作室",
                src: "https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3",
                cover: "https://images.unsplash.com/photo-1511671782779-c97d3d27a1d4?auto=format&fit=crop&w=500&q=80",
                type: "audio",
                lyrics: "[00:00.00] 水乡谣 - 百灵鸟艺术工作室\n[00:05.00] 演唱：琬婷\n[00:12.50] 沭河岸边水波荡漾\n[00:18.20] 百灵鸟在芦苇荡歌唱\n[00:25.10] 琴声悠扬穿过马陵山\n[00:32.00] 带着梦飞向远方"
            },
            {
                title: "马陵山的百灵鸟 (演示MV)",
                artist: "小雨 & 琬婷",
                src: "https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerBlazes.mp4",
                cover: "https://images.unsplash.com/photo-1470225620780-dba8ba36b745?auto=format&fit=crop&w=500&q=80",
                type: "video",
                lyrics: "[00:00.00] 马陵山的百灵鸟 (MV演示)\n[00:03.00] 巍巍马陵山下\n[00:08.50] 歌声亮丽如霞\n[00:14.20] 纯真的笑脸映着阳光\n[00:20.00] 唱出心中的梦想"
            }
        ];

        let currentIndex = 0, isAdmin = false, isPlaying = false, isShuffle = false, isLoop = false, visMode = 'bars', parsedLyrics = [];
        const activeAudio = new Audio();
        activeAudio.crossOrigin = "anonymous";
        let audioCtx, analyser, audioSource, videoSource;

        const videoElement = document.getElementById('videoElement');
        const audioView = document.getElementById('audioView');
        const currentCover = document.getElementById('currentCover');
        const bgCoverBlur = document.getElementById('bgCoverBlur');
        const currentTitle = document.getElementById('currentTitle');
        const currentArtist = document.getElementById('currentArtist');
        const playPauseIcon = document.getElementById('playPauseIcon');
        const progressBar = document.getElementById('progressBar');
        const volumeBar = document.getElementById('volumeBar');
        const muteBtn = document.getElementById('muteBtn');
        const currentTimeEl = document.getElementById('currentTime');
        const durationEl = document.getElementById('duration');
        const playlistContainer = document.getElementById('playlistContainer');
        const lyricsContainer = document.getElementById('lyricsContainer');
        const lyricStatus = document.getElementById('lyricStatus');
        const uploadBtn = document.getElementById('uploadBtn');
        const modalOverlay = document.getElementById('modalOverlay');
        const roleDot = document.getElementById('roleDot');
        const roleText = document.getElementById('roleText');
        const roleBtnText = document.getElementById('roleBtnText');
        const visualizerCanvas = document.getElementById('visualizerCanvas');
        const speedSelect = document.getElementById('speedSelect');
        const pipBtn = document.getElementById('pipBtn');

        function init() {
            renderPlaylist();
            loadTrack(currentIndex);
            activeAudio.addEventListener('timeupdate', updateProgress);
            activeAudio.addEventListener('ended', handleEnded);
            videoElement.addEventListener('timeupdate', updateProgress);
            videoElement.addEventListener('ended', handleEnded);

            volumeBar.addEventListener('input', (e) => {
                activeAudio.volume = e.target.value;
                videoElement.volume = e.target.value;
            });

            progressBar.addEventListener('input', (e) => {
                const time = (e.target.value / 100) * getDuration();
                if (playlist[currentIndex].type === 'video') videoElement.currentTime = time;
                else activeAudio.currentTime = time;
            });
        }

        function setupAudioContext() {
            if (audioCtx) return;
            try {
                window.AudioContext = window.AudioContext || window.webkitAudioContext;
                audioCtx = new AudioContext();
                analyser = audioCtx.createAnalyser();
                analyser.fftSize = 128;
                audioSource = audioCtx.createMediaElementSource(activeAudio);
                audioSource.connect(analyser);
                videoSource = audioCtx.createMediaElementSource(videoElement);
                videoSource.connect(analyser);
                analyser.connect(audioCtx.destination);
                drawVisualizer();
            } catch(e) {}
        }

        function renderPlaylist() {
            playlistContainer.innerHTML = '';
            playlist.forEach((track, index) => {
                const isActive = index === currentIndex;
                const div = document.createElement('div');
                div.className = `playlist-item ${isActive ? 'active' : ''}`;
                div.innerHTML = `
                    <img src="${track.cover}" alt="cover">
                    <div class="playlist-info" onclick="selectTrack(${index})">
                        <h4>${track.title}</h4>
                        <p>${track.artist} · <span style="text-transform:uppercase">${track.type}</span></p>
                    </div>
                    <div class="item-actions">
                        <button onclick="selectTrack(${index})" class="icon-btn" title="播放"><i class="fa-solid fa-play"></i></button>
                        ${isAdmin ? `
                            <button onclick="openEditModal(${index}, event)" class="icon-btn edit" title="编辑曲目"><i class="fa-solid fa-pen"></i></button>
                            <button onclick="deleteTrack(${index}, event)" class="icon-btn delete" title="删除曲目"><i class="fa-solid fa-trash"></i></button>
                        ` : ''}
                    </div>
                `;
                playlistContainer.appendChild(div);
            });
            localStorage.setItem('lark_playlist', JSON.stringify(playlist));
        }

        function loadTrack(index) {
            if (index < 0 || index >= playlist.length) return;
            currentIndex = index;
            const track = playlist[currentIndex];
            currentTitle.textContent = track.title;
            currentArtist.textContent = track.artist;
            currentCover.src = track.cover;
            bgCoverBlur.style.backgroundImage = `url(${track.cover})`;
            parseLyrics(track.lyrics || '');

            if (track.type === 'video') {
                videoElement.src = track.src;
                videoElement.load();
                videoElement.classList.remove('hidden');
                audioView.classList.add('hidden');
                pipBtn.classList.remove('hidden');
            } else {
                activeAudio.src = track.src;
                activeAudio.load();
                videoElement.classList.add('hidden');
                audioView.classList.remove('hidden');
                pipBtn.classList.add('hidden');
            }
            renderPlaylist();
        }

        function selectTrack(index) {
            loadTrack(index);
            playMedia();
        }

        function togglePlay() {
            setupAudioContext();
            if (audioCtx && audioCtx.state === 'suspended') audioCtx.resume();
            if (isPlaying) pauseMedia();
            else playMedia();
        }

        function playMedia() {
            setupAudioContext();
            if (audioCtx && audioCtx.state === 'suspended') audioCtx.resume();
            isPlaying = true;
            playPauseIcon.className = "fa-solid fa-pause";
            const currentTrack = playlist[currentIndex];
            if (currentTrack && currentTrack.type === 'video') {
                videoElement.play().catch(()=>{});
            } else {
                activeAudio.play().catch(()=>{});
            }
        }

        function pauseMedia() {
            isPlaying = false;
            playPauseIcon.className = "fa-solid fa-play";
            if (playlist[currentIndex].type === 'video') videoElement.pause();
            else activeAudio.pause();
        }

        function nextTrack() {
            if (isShuffle) currentIndex = Math.floor(Math.random() * playlist.length);
            else currentIndex = (currentIndex + 1) % playlist.length;
            loadTrack(currentIndex);
            playMedia();
        }

        function prevTrack() {
            currentIndex = (currentIndex - 1 + playlist.length) % playlist.length;
            loadTrack(currentIndex);
            playMedia();
        }

        function handleEnded() {
            if (isLoop) playMedia();
            else nextTrack();
        }

        function updateProgress() {
            const current = playlist[currentIndex].type === 'video' ? videoElement.currentTime : activeAudio.currentTime;
            const duration = getDuration();
            if (duration > 0) {
                progressBar.value = (current / duration) * 100;
                currentTimeEl.textContent = formatTime(current);
                durationEl.textContent = formatTime(duration);
            }
            updateLyrics(current);
        }

        function getDuration() {
            return playlist[currentIndex].type === 'video' ? (videoElement.duration || 0) : (activeAudio.duration || 0);
        }

        function formatTime(sec) {
            const m = Math.floor(sec / 60);
            const s = Math.floor(sec % 60);
            return `${m.toString().padStart(2, '0')}:${s.toString().padStart(2, '0')}`;
        }

        function parseLyrics(text) {
            parsedLyrics = [];
            lyricsContainer.innerHTML = '';
            if (!text.trim()) {
                lyricStatus.textContent = "暂无歌词";
                lyricsContainer.innerHTML = `<p style="color:#64748b; font-size:12px; margin-top:20px;">暂无同步歌词</p>`;
                return;
            }
            lyricStatus.textContent = "已加载同步歌词";
            const lines = text.split('\n');
            const reg = /\[(\d{2}):(\d{2})\.(\d{2,3})\]/;
            lines.forEach(l => {
                const m = reg.exec(l);
                if (m) {
                    const time = parseInt(m[1]) * 60 + parseInt(m[2]) + parseInt(m[3]) / 1000;
                    const content = l.replace(reg, '').trim();
                    if (content) parsedLyrics.push({ time, content });
                }
            });

            if (parsedLyrics.length > 0) {
                parsedLyrics.forEach((item) => {
                    const p = document.createElement('p');
                    p.className = 'lyric-line';
                    p.textContent = item.content;
                    p.onclick = () => seekTo(item.time);
                    lyricsContainer.appendChild(p);
                });
            } else {
                lyricsContainer.innerHTML = `<p style="font-size:12px; color:#cbd5e1; white-space:pre-line;">${text}</p>`;
            }
        }

        function updateLyrics(time) {
            if (parsedLyrics.length === 0) return;
            let activeIdx = -1;
            for (let i = 0; i < parsedLyrics.length; i++) {
                if (time >= parsedLyrics[i].time) activeIdx = i;
                else break;
            }
            const lines = lyricsContainer.getElementsByClassName('lyric-line');
            Array.from(lines).forEach((el, idx) => {
                if (idx === activeIdx) {
                    el.classList.add('active');
                    el.scrollIntoView({ behavior: 'smooth', block: 'center' });
                } else {
                    el.classList.remove('active');
                }
            });
        }

        function seekTo(time) {
            if (playlist[currentIndex].type === 'video') videoElement.currentTime = time;
            else activeAudio.currentTime = time;
        }

        function setVisMode(m) {
            visMode = m;
            document.querySelectorAll('.vis-btn').forEach(b => b.style.color = '#cbd5e1');
            event.target.style.color = '#38bdf8';
        }

        function drawVisualizer() {
            requestAnimationFrame(drawVisualizer);
            if (!analyser) return;
            const canvas = visualizerCanvas;
            const ctx = canvas.getContext('2d');
            canvas.width = canvas.parentElement.clientWidth;
            canvas.height = canvas.parentElement.clientHeight;

            const bufferLength = analyser.frequencyBinCount;
            const data = new Uint8Array(bufferLength);
            analyser.getByteFrequencyData(data);
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            if (visMode === 'bars') {
                const barW = (canvas.width / bufferLength) * 2.5;
                let x = 0;
                for (let i = 0; i < bufferLength; i++) {
                    const h = (data[i] / 255) * canvas.height * 0.75;
                    ctx.fillStyle = 'rgba(56, 189, 248, 0.5)';
                    ctx.fillRect(x, canvas.height - h, barW, h);
                    x += barW + 1;
                }
            } else if (visMode === 'wave') {
                analyser.getByteTimeDomainData(data);
                ctx.lineWidth = 2;
                ctx.strokeStyle = '#38bdf8';
                ctx.beginPath();
                const sliceW = canvas.width / bufferLength;
                let x = 0;
                for (let i = 0; i < bufferLength; i++) {
                    const v = data[i] / 128.0;
                    const y = (v * canvas.height) / 2;
                    if (i === 0) ctx.moveTo(x, y); else ctx.lineTo(x, y);
                    x += sliceW;
                }
                ctx.stroke();
            } else if (visMode === 'circle') {
                const cx = canvas.width / 2, cy = canvas.height / 2, r = Math.min(cx, cy) * 0.55;
                ctx.save(); ctx.translate(cx, cy);
                for (let i = 0; i < bufferLength; i++) {
                    const angle = (i * 2 * Math.PI) / bufferLength;
                    const h = (data[i] / 255) * 50;
                    const x1 = Math.cos(angle) * r, y1 = Math.sin(angle) * r;
                    const x2 = Math.cos(angle) * (r + h), y2 = Math.sin(angle) * (r + h);
                    ctx.strokeStyle = `rgba(56, 189, 248, ${data[i]/255})`;
                    ctx.lineWidth = 2;
                    ctx.beginPath(); ctx.moveTo(x1, y1); ctx.lineTo(x2, y2); ctx.stroke();
                }
                ctx.restore();
            }
        }

        function toggleRole() {
            isAdmin = !isAdmin;
            if (isAdmin) {
                roleDot.className = "role-dot admin";
                roleText.textContent = "管理员模式 (可上传/编辑/删除)";
                roleBtnText.textContent = "切换为普通用户";
                uploadBtn.classList.remove('hidden');
            } else {
                roleDot.className = "role-dot user";
                roleText.textContent = "普通用户模式";
                roleBtnText.textContent = "切换为管理员";
                uploadBtn.classList.add('hidden');
            }
            renderPlaylist();
        }

        function openModal() {
            if (!isAdmin) return;
            document.getElementById('modalTitle').textContent = "管理员上传曲目";
            document.getElementById('editIndex').value = "-1";
            document.getElementById('mediaForm').reset();
            modalOverlay.style.display = "flex";
        }

        function openEditModal(index, e) {
            e.stopPropagation();
            if (!isAdmin) return;
            const t = playlist[index];
            document.getElementById('modalTitle').textContent = "编辑曲目信息";
            document.getElementById('editIndex').value = index;
            document.getElementById('inputTitle').value = t.title;
            document.getElementById('inputArtist').value = t.artist;
            document.getElementById('inputLyrics').value = t.lyrics || '';
            modalOverlay.style.display = "flex";
        }

        function closeModal() {
            modalOverlay.style.display = "none";
        }

        function handleFormSubmit(e) {
            e.preventDefault();
            const editIdx = parseInt(document.getElementById('editIndex').value);
            const title = document.getElementById('inputTitle').value;
            const artist = document.getElementById('inputArtist').value;
            const lyrics = document.getElementById('inputLyrics').value;
            const fileInput = document.getElementById('inputFile');
            const coverInput = document.getElementById('inputCover');

            if (editIdx === -1 && (!fileInput.files || !fileInput.files[0])) {
                alert("请先选择要上传的音频或视频文件！");
                return;
            }

            const processSave = (src, cover, type) => {
                if (editIdx >= 0) {
                    playlist[editIdx] = { ...playlist[editIdx], title, artist, lyrics, src, cover, type };
                    currentIndex = editIdx;
                } else {
                    playlist.push({ title, artist, src, cover, type, lyrics });
                    currentIndex = playlist.length - 1;
                }
                closeModal();
                loadTrack(currentIndex);
                playMedia();
            };

            let existingTrack = editIdx >= 0 ? playlist[editIdx] : null;
            let src = existingTrack ? existingTrack.src : "https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3";
            let cover = existingTrack ? existingTrack.cover : "https://images.unsplash.com/photo-1514525253161-7a46d19cd819?auto=format&fit=crop&w=500&q=80";
            let type = existingTrack ? existingTrack.type : "audio";

            const handleCoverAndSave = () => {
                if (coverInput.files && coverInput.files[0]) {
                    const reader = new FileReader();
                    reader.onload = function(evt) { processSave(src, evt.target.result, type); };
                    reader.readAsDataURL(coverInput.files[0]);
                } else {
                    processSave(src, cover, type);
                }
            };

            if (fileInput.files && fileInput.files[0]) {
                const f = fileInput.files[0];
                type = f.type.startsWith('video') ? 'video' : 'audio';
                const reader = new FileReader();
                reader.onload = function(evt) {
                    src = evt.target.result;
                    handleCoverAndSave();
                };
                reader.readAsDataURL(f);
            } else {
                handleCoverAndSave();
            }
        }

        function deleteTrack(index, e) {
            e.stopPropagation();
            if (!isAdmin) return;
            if (confirm(`确定要删除曲目《${playlist[index].title}》吗？`)) {
                playlist.splice(index, 1);
                if (currentIndex >= playlist.length) currentIndex = Math.max(0, playlist.length - 1);
                renderPlaylist();
                if (playlist.length > 0) loadTrack(currentIndex);
            }
        }

        function toggleShuffle() {
            isShuffle = !isShuffle;
            document.getElementById('shuffleBtn').style.color = isShuffle ? '#38bdf8' : '#cbd5e1';
        }

        function toggleLoop() {
            isLoop = !isLoop;
            document.getElementById('loopBtn').style.color = isLoop ? '#38bdf8' : '#cbd5e1';
        }

        function toggleMute() {
            activeAudio.muted = !activeAudio.muted;
            videoElement.muted = !videoElement.muted;
            muteBtn.innerHTML = activeAudio.muted ? '<i class="fa-solid fa-volume-xmark" style="color:#e11d48"></i>' : '<i class="fa-solid fa-volume-high"></i>';
        }

        function changeSpeed() {
            const s = parseFloat(speedSelect.value);
            activeAudio.playbackRate = s;
            videoElement.playbackRate = s;
        }

        function togglePiP() {
            if (document.pictureInPictureElement) document.exitPictureInPicture();
            else if (videoElement.readyState >= 1) videoElement.requestPictureInPicture();
        }

        function toggleFullscreen() {
            const container = videoElement.parentElement;
            if (!document.fullscreenElement) container.requestFullscreen();
            else document.exitFullscreen();
        }

        window.onload = init;
    </script>
</body>
</html>
