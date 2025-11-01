/* BahaApp: {"id": "audio-visualizer", "name": "Аудио плеер", "icon": "🎵", "category": "music"} */
function() {
    return `
        <div style="height: 100%; display: flex; flex-direction: column; background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%); color: white;">
            <div style="padding: 25px; text-align: center; background: rgba(0,0,0,0.3);">
                <h2 style="margin: 0 0 10px 0; font-size: 24px;">🎵 Аудио плеер</h2>
                <p style="margin: 0; opacity: 0.8;">Загрузите свои аудиофайлы для воспроизведения</p>
            </div>
            
            <div style="flex-grow: 1; padding: 20px; display: flex; flex-direction: column; gap: 20px;">
                <!-- Визуализатор -->
                <div style="flex-grow: 1; background: rgba(0,0,0,0.2); border-radius: 15px; padding: 20px; display: flex; align-items: end; gap: 2px; height: 200px;"
                     id="visualizer">
                    ${Array.from({length: 50}, () => 
                        '<div style="flex-grow: 1; background: linear-gradient(to top, #667eea, #764ba2); border-radius: 2px 2px 0 0; min-height: 5px; transition: height 0.1s;"></div>'
                    ).join('')}
                </div>
                
                <!-- Информация о треке -->
                <div style="background: rgba(255,255,255,0.05); border-radius: 15px; padding: 20px; text-align: center;">
                    <div id="track-info" style="margin-bottom: 15px;">
                        <div style="font-size: 18px; font-weight: 500; margin-bottom: 5px;" id="track-title">Трек не выбран</div>
                        <div style="font-size: 14px; opacity: 0.7;" id="track-artist">Baha OS Player</div>
                    </div>
                    
                    <!-- Прогресс бар -->
                    <div style="margin-bottom: 15px;">
                        <div style="display: flex; justify-content: space-between; font-size: 12px; opacity: 0.7; margin-bottom: 5px;">
                            <span id="current-time">0:00</span>
                            <span id="duration">0:00</span>
                        </div>
                        <div style="width: 100%; height: 4px; background: rgba(255,255,255,0.2); border-radius: 2px; cursor: pointer;" id="progress-bar">
                            <div style="width: 0%; height: 100%; background: linear-gradient(90deg, #667eea, #764ba2); border-radius: 2px; transition: width 0.1s;" id="progress"></div>
                        </div>
                    </div>
                    
                    <!-- Элементы управления -->
                    <div style="display: flex; justify-content: center; align-items: center; gap: 20px;">
                        <button class="control-btn" onclick="previousTrack()" style="font-size: 20px;">⏮</button>
                        <button class="control-btn play" onclick="togglePlay()" id="play-btn" 
                                style="width: 60px; height: 60px; font-size: 24px; background: linear-gradient(135deg, #667eea, #764ba2);">▶</button>
                        <button class="control-btn" onclick="nextTrack()" style="font-size: 20px;">⏭</button>
                    </div>
                </div>
                
                <!-- Громкость -->
                <div style="display: flex; align-items: center; gap: 15px; background: rgba(255,255,255,0.05); border-radius: 10px; padding: 15px;">
                    <span style="font-size: 18px;">🔊</span>
                    <input type="range" id="volume" min="0" max="100" value="50" 
                           style="flex-grow: 1; height: 4px; background: rgba(255,255,255,0.2); border-radius: 2px; outline: none;"
                           oninput="changeVolume(this.value)">
                    <span id="volume-value" style="font-size: 14px; opacity: 0.8;">50%</span>
                </div>
            </div>
            
            <!-- Плейлист -->
            <div style="background: rgba(30,30,40,0.8); border-top: 1px solid rgba(255,255,255,0.1); max-height: 200px; overflow-y: auto;">
                <div style="padding: 15px 20px; display: flex; justify-content: space-between; align-items: center;">
                    <h4 style="margin: 0; font-size: 16px;">Плейлист</h4>
                    <div>
                        <button class="toolbar-btn" onclick="document.getElementById('file-input').click()" style="padding: 8px 16px; margin-right: 10px;">
                            📁 Добавить треки
                        </button>
                        <button class="toolbar-btn" onclick="clearPlaylist()" style="padding: 8px 16px;">
                            🗑️ Очистить
                        </button>
                    </div>
                </div>
                
                <div id="playlist" style="padding: 0 20px 15px;">
                    <!-- Треки будут здесь -->
                    <div style="padding: 15px; text-align: center; opacity: 0.7; font-style: italic;">
                        Плейлист пуст. Добавьте аудиофайлы для воспроизведения.
                    </div>
                </div>
            </div>
            
            <input type="file" id="file-input" accept="audio/*" multiple style="display: none;" onchange="handleFileSelect(event)">
            <audio id="audio-player" style="display: none;"></audio>
        </div>
        <script>
            let audioPlayer = document.getElementById('audio-player');
            let playlist = [];
            let currentTrackIndex = -1;
            let isPlaying = false;
            let audioContext;
            let analyser;
            let dataArray;
            let bufferLength;
            let animationId;
            
            // Инициализация аудио контекста для визуализации
            function initAudioContext() {
                audioContext = new (window.AudioContext || window.webkitAudioContext)();
                analyser = audioContext.createAnalyser();
                analyser.fftSize = 256;
                bufferLength = analyser.frequencyBinCount;
                dataArray = new Uint8Array(bufferLength);
                
                const source = audioContext.createMediaElementSource(audioPlayer);
                source.connect(analyser);
                analyser.connect(audioContext.destination);
            }
            
            // Визуализация аудио
            function visualize() {
                if (!analyser) return;
                
                analyser.getByteFrequencyData(dataArray);
                const bars = document.querySelectorAll('#visualizer > div');
                
                for (let i = 0; i < bars.length; i++) {
                    const value = dataArray[i * Math.floor(bufferLength / bars.length)] / 255;
                    const height = Math.max(5, value * 100);
                    bars[i].style.height = height + '%';
                    
                    // Цвет в зависимости от высоты
                    const hue = 260 + (value * 40);
                    bars[i].style.background = \`linear-gradient(to top, hsl(\${hue}, 70%, 60%), hsl(\${hue}, 70%, 40%))\`;
                }
                
                animationId = requestAnimationFrame(visualize);
            }
            
            // Обработка выбора файлов
            function handleFileSelect(event) {
                const files = event.target.files;
                for (let file of files) {
                    if (file.type.startsWith('audio/')) {
                        const url = URL.createObjectURL(file);
                        playlist.push({
                            name: file.name.replace(/\\.[^/.]+$/, ""),
                            url: url,
                            file: file
                        });
                    }
                }
                updatePlaylist();
                if (currentTrackIndex === -1 && playlist.length > 0) {
                    playTrack(0);
                }
            }
            
            // Обновление плейлиста
            function updatePlaylist() {
                const playlistElement = document.getElementById('playlist');
                playlistElement.innerHTML = '';
                
                playlist.forEach((track, index) => {
                    const trackElement = document.createElement('div');
                    trackElement.style.cssText = \`
                        padding: 12px 15px;
                        margin-bottom: 5px;
                        background: \${index === currentTrackIndex ? 'rgba(102, 126, 234, 0.2)' : 'rgba(255,255,255,0.05)'};
                        border-radius: 8px;
                        cursor: pointer;
                        display: flex;
                        justify-content: space-between;
                        align-items: center;
                        border-left: 3px solid \${index === currentTrackIndex ? '#667eea' : 'transparent'};
                        transition: all 0.2s ease;
                    \`;
                    
                    trackElement.innerHTML = \`
                        <div>
                            <div style="font-weight: 500; margin-bottom: 3px;">\${track.name}</div>
                            <div style="font-size: 11px; opacity: 0.6;">\${formatFileSize(track.file.size)}</div>
                        </div>
                        <div style="font-size: 12px; opacity: 0.7;">
                            \${index === currentTrackIndex ? (isPlaying ? '▶ Воспроизведение' : '⏸ Пауза') : ''}
                        </div>
                    \`;
                    
                    trackElement.addEventListener('click', () => playTrack(index));
                    playlistElement.appendChild(trackElement);
                });
            }
            
            // Форматирование размера файла
            function formatFileSize(bytes) {
                if (bytes === 0) return '0 Bytes';
                const k = 1024;
                const sizes = ['Bytes', 'KB', 'MB', 'GB'];
                const i = Math.floor(Math.log(bytes) / Math.log(k));
                return parseFloat((bytes / Math.pow(k, i)).toFixed(2)) + ' ' + sizes[i];
            }
            
            // Воспроизведение трека
            function playTrack(index) {
                if (index < 0 || index >= playlist.length) return;
                
                currentTrackIndex = index;
                const track = playlist[index];
                
                audioPlayer.src = track.url;
                audioPlayer.load();
                
                document.getElementById('track-title').textContent = track.name;
                document.getElementById('track-artist').textContent = 'Загруженный трек';
                
                if (!audioContext) {
                    initAudioContext();
                }
                
                audioPlayer.play().then(() => {
                    isPlaying = true;
                    document.getElementById('play-btn').textContent = '⏸';
                    updatePlaylist();
                    visualize();
                    updateProgress();
                }).catch(error => {
                    console.error('Ошибка воспроизведения:', error);
                });
            }
            
            // Переключение воспроизведения/паузы
            function togglePlay() {
                if (!audioPlayer.src) {
                    if (playlist.length > 0) {
                        playTrack(0);
                    }
                    return;
                }
                
                if (isPlaying) {
                    audioPlayer.pause();
                    document.getElementById('play-btn').textContent = '▶';
                    cancelAnimationFrame(animationId);
                } else {
                    audioPlayer.play();
                    document.getElementById('play-btn').textContent = '⏸';
                    visualize();
                }
                isPlaying = !isPlaying;
                updatePlaylist();
            }
            
            // Следующий трек
            function nextTrack() {
                if (playlist.length === 0) return;
                const nextIndex = (currentTrackIndex + 1) % playlist.length;
                playTrack(nextIndex);
            }
            
            // Предыдущий трек
            function previousTrack() {
                if (playlist.length === 0) return;
                const prevIndex = (currentTrackIndex - 1 + playlist.length) % playlist.length;
                playTrack(prevIndex);
            }
            
            // Изменение громкости
            function changeVolume(value) {
                audioPlayer.volume = value / 100;
                document.getElementById('volume-value').textContent = value + '%';
            }
            
            // Очистка плейлиста
            function clearPlaylist() {
                playlist = [];
                currentTrackIndex = -1;
                audioPlayer.src = '';
                isPlaying = false;
                document.getElementById('play-btn').textContent = '▶';
                document.getElementById('track-title').textContent = 'Трек не выбран';
                document.getElementById('track-artist').textContent = 'Baha OS Player';
                updatePlaylist();
                cancelAnimationFrame(animationId);
            }
            
            // Обновление прогресса
            function updateProgress() {
                if (!audioPlayer.src) return;
                
                const progress = document.getElementById('progress');
                const currentTime = document.getElementById('current-time');
                const duration = document.getElementById('duration');
                
                const update = () => {
                    if (audioPlayer.duration) {
                        const percent = (audioPlayer.currentTime / audioPlayer.duration) * 100;
                        progress.style.width = percent + '%';
                        
                        currentTime.textContent = formatTime(audioPlayer.currentTime);
                        duration.textContent = formatTime(audioPlayer.duration);
                    }
                    
                    if (isPlaying) {
                        requestAnimationFrame(update);
                    }
                };
                
                update();
            }
            
            // Форматирование времени
            function formatTime(seconds) {
                const mins = Math.floor(seconds / 60);
                const secs = Math.floor(seconds % 60);
                return \`\${mins}:\${secs < 10 ? '0' : ''}\${secs}\`;
            }
            
            // Обработчики событий аудио
            audioPlayer.addEventListener('loadedmetadata', updateProgress);
            audioPlayer.addEventListener('ended', nextTrack);
            
            // Обработчик прогресс бара
            document.getElementById('progress-bar').addEventListener('click', function(e) {
                if (!audioPlayer.src) return;
                
                const rect = this.getBoundingClientRect();
                const percent = (e.clientX - rect.left) / rect.width;
                audioPlayer.currentTime = percent * audioPlayer.duration;
            });
            
            // Стили для кнопок управления
            const style = document.createElement('style');
            style.textContent = \`
                .control-btn {
                    width: 45px;
                    height: 45px;
                    border-radius: 50%;
                    background: rgba(255,255,255,0.1);
                    border: none;
                    color: white;
                    cursor: pointer;
                    display: flex;
                    align-items: center;
                    justify-content: center;
                    transition: all 0.2s ease;
                    font-size: 16px;
                }
                .control-btn:hover {
                    background: rgba(255,255,255,0.2);
                    transform: scale(1.05);
                }
                .control-btn.play {
                    background: linear-gradient(135deg, #667eea, #764ba2);
                    width: 60px;
                    height: 60px;
                    font-size: 20px;
                }
                .control-btn.play:hover {
                    background: linear-gradient(135deg, #5a6fd8, #6a4190);
                    transform: scale(1.1);
                }
            \`;
            document.head.appendChild(style);
        </script>
    `;
}
