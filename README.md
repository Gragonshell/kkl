<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>梦幻照片墙</title>
    <!-- 引入外部资源 -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css" rel="stylesheet">
    
    <!-- 配置Tailwind -->
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        primary: '#8a5cf7',
                        secondary: '#ec4899',
                    },
                    animation: {
                        'float': 'float 6s ease-in-out infinite',
                        'fade-in': 'fadeIn 0.8s ease-out forwards',
                        'slide-up': 'slideUp 0.6s ease-out forwards',
                        'pulse-slow': 'pulse 4s cubic-bezier(0.4, 0, 0.6, 1) infinite',
                    },
                    keyframes: {
                        float: {
                            '0%, 100%': { transform: 'translateY(0)' },
                            '50%': { transform: 'translateY(-15px)' },
                        },
                        fadeIn: {
                            '0%': { opacity: '0' },
                            '100%': { opacity: '1' },
                        },
                        slideUp: {
                            '0%': { transform: 'translateY(30px)', opacity: '0' },
                            '100%': { transform: 'translateY(0)', opacity: '1' },
                        }
                    }
                }
            }
        }
    </script>
    
    <style type="text/tailwindcss">
        @layer utilities {
            .photo-grid {
                display: grid;
                grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
                gap: 1.5rem;
            }
            .glass-effect {
                backdrop-filter: blur(12px);
                background-color: rgba(255, 255, 255, 0.7);
            }
            .text-shadow {
                text-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            }
        }
    </style>
    
    <style>
        /* 梦幻背景 */
        .dreamy-bg {
            background: linear-gradient(135deg, #fdfcfb 0%, #e2d1c3 100%);
            background-attachment: fixed;
            position: relative;
        }
        
        .dreamy-bg::before,
        .dreamy-bg::after {
            content: '';
            position: fixed;
            border-radius: 50%;
            z-index: 0;
            filter: blur(80px);
            opacity: 0.4;
        }
        
        .dreamy-bg::before {
            width: 600px;
            height: 600px;
            background: #8a5cf7;
            top: -300px;
            right: -100px;
            animation: move 25s ease-in-out infinite;
        }
        
        .dreamy-bg::after {
            width: 500px;
            height: 500px;
            background: #ec4899;
            bottom: -250px;
            left: -150px;
            animation: move 30s ease-in-out infinite 5s;
        }
        
        @keyframes move {
            0%, 100% { transform: translate(0, 0); }
            50% { transform: translate(50px, 50px); }
        }
        
        /* 照片样式 */
        .photo-item {
            transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            opacity: 0;
        }
        
        .photo-container {
            position: relative;
            overflow: hidden;
            border-radius: 0.75rem;
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.05), 0 8px 10px -6px rgba(0, 0, 0, 0.02);
        }
        
        .photo-img {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: block;
            transition: transform 0.7s ease;
        }
        
        .photo-item:hover .photo-img {
            transform: scale(1.05);
        }
        
        .photo-item:hover {
            transform: translateY(-8px) scale(1.02);
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
        }
        
        /* 平滑滚动 */
        html {
            scroll-behavior: smooth;
        }
        
        /* 模态框动画 */
        #photoModal {
            opacity: 0;
            visibility: hidden;
            transition: opacity 0.3s, visibility 0.3s;
        }
        
        #photoModal.active {
            opacity: 1;
            visibility: visible;
        }
        
        #modalImage {
            transform: scale(0.95);
            transition: transform 0.3s;
        }
        
        #photoModal.active #modalImage {
            transform: scale(1);
        }
    </style>
</head>
<body class="dreamy-bg min-h-screen">
    <!-- 顶部导航 -->
    <header class="glass-effect shadow-sm sticky top-0 z-30 transition-all duration-300">
        <div class="container mx-auto px-4 py-4 flex justify-between items-center">
            <h1 class="text-2xl font-bold text-gray-800 text-shadow flex items-center">
                <i class="fa fa-camera-retro text-primary mr-2 animate-pulse-slow"></i>
                梦幻照片墙
            </h1>
            <div class="flex items-center space-x-4">
                <label for="uploadInput" class="cursor-pointer bg-primary hover:bg-primary/90 text-white px-5 py-2.5 rounded-lg transition-all flex items-center shadow-lg hover:shadow-primary/30 hover:shadow-xl transform hover:-translate-y-0.5">
                    <i class="fa fa-plus-circle mr-2"></i> 添加照片
                </label>
                <input type="file" id="uploadInput" accept="image/*" multiple class="hidden">
            </div>
        </div>
    </header>

    <!-- 主要内容区 -->
    <main class="container mx-auto px-4 py-12 relative z-10">
        <!-- 说明文字 -->
        <div class="text-center mb-12 max-w-2xl mx-auto animate-slide-up">
            <h2 class="text-[clamp(1.5rem,3vw,2.5rem)] font-bold text-gray-800 mb-4 text-shadow">珍藏每一个美好瞬间</h2>
            <p class="text-gray-600">上传照片，它们会以精美的动画效果展示在这里，成为您独特的回忆墙</p>
        </div>
        
        <!-- 照片网格 -->
        <div id="photoGrid" class="photo-grid">
            <!-- 照片会通过JavaScript动态添加到这里 -->
        </div>
        
        <!-- 空状态提示 -->
        <div id="emptyState" class="text-center py-20 animate-fade-in">
            <div class="w-24 h-24 bg-primary/10 rounded-full flex items-center justify-center mx-auto mb-6 animate-float">
                <i class="fa fa-picture-o text-4xl text-primary"></i>
            </div>
            <h3 class="text-xl font-medium text-gray-700 mb-2">还没有照片</h3>
            <p class="text-gray-500">点击上方的"添加照片"按钮，上传您的第一张照片吧</p>
        </div>
    </main>

    <!-- 照片查看模态框 -->
    <div id="photoModal" class="fixed inset-0 bg-black/90 z-50 flex items-center justify-center p-4">
        <button id="closeModal" class="absolute top-6 right-6 text-white text-2xl hover:text-gray-300 transition-colors">
            <i class="fa fa-times"></i>
        </button>
        <img id="modalImage" src="" alt="照片大图" class="max-h-[90vh] max-w-full object-contain">
    </div>

    <script>
        // DOM元素
        const uploadInput = document.getElementById('uploadInput');
        const photoGrid = document.getElementById('photoGrid');
        const emptyState = document.getElementById('emptyState');
        const photoModal = document.getElementById('photoModal');
        const modalImage = document.getElementById('modalImage');
        const closeModal = document.getElementById('closeModal');
        
        // 检查是否有保存的照片
        let savedPhotos = JSON.parse(localStorage.getItem('dreamyPhotos')) || [];
        
        // 初始化页面
        init();
        
        function init() {
            // 从本地存储加载照片
            if (savedPhotos.length > 0) {
                savedPhotos.forEach((photo, index) => {
                    // 错开动画时间，创造流动感
                    setTimeout(() => {
                        addPhotoToGrid(photo.url, photo.alt);
                    }, index * 100);
                });
            }
            
            checkEmptyState();
        }
        
        // 监听文件上传
        uploadInput.addEventListener('change', handleFileUpload);
        
        // 处理文件上传
        function handleFileUpload(e) {
            const files = e.target.files;
            if (!files.length) return;
            
            for (let i = 0; i < files.length; i++) {
                const file = files[i];
                if (!file.type.startsWith('image/')) continue;
                
                const reader = new FileReader();
                
                reader.onload = function(event) {
                    // 保存照片到本地存储
                    const photo = {
                        url: event.target.result,
                        alt: `照片 ${new Date().toLocaleString()}`,
                        date: new Date().toISOString()
                    };
                    
                    savedPhotos.push(photo);
                    localStorage.setItem('dreamyPhotos', JSON.stringify(savedPhotos));
                    
                    // 添加到网格，带动画
                    addPhotoToGrid(photo.url, photo.alt);
                    checkEmptyState();
                };
                
                reader.readAsDataURL(file);
            }
            
            // 重置输入，允许重复选择同一文件
            uploadInput.value = '';
        }
        
        // 添加照片到网格
        function addPhotoToGrid(url, alt) {
            // 随机照片比例，创造错落有致的效果
            const aspectRatios = [
                'square', // 1:1
                '4/3',    // 4:3
                '3/4',    // 3:4
                '16/9',   // 16:9
                '9/16'    // 9:16
            ];
            const randomAspect = aspectRatios[Math.floor(Math.random() * aspectRatios.length)];
            
            const photoItem = document.createElement('div');
            photoItem.className = `photo-item relative`;
            photoItem.classList.add(`aspect-${randomAspect}`);
            
            const photoContainer = document.createElement('div');
            photoContainer.className = 'photo-container h-full';
            
            const img = document.createElement('img');
            img.src = url;
            img.alt = alt;
            img.className = 'photo-img';
            
            // 图片加载完成后显示动画
            img.onload = function() {
                photoItem.style.animation = 'fadeIn 0.8s ease-out forwards, slideUp 0.6s ease-out forwards';
            };
            
            // 点击查看大图
            photoContainer.addEventListener('click', () => {
                modalImage.src = url;
                modalImage.alt = alt;
                photoModal.classList.add('active');
                document.body.style.overflow = 'hidden';
            });
            
            // 右键删除照片
            photoContainer.addEventListener('contextmenu', (e) => {
                e.preventDefault();
                if (confirm('确定要删除这张照片吗？')) {
                    // 移除动画
                    photoItem.style.opacity = '0';
                    photoItem.style.transform = 'translateY(20px)';
                    
                    // 延迟删除，等待动画完成
                    setTimeout(() => {
                        // 从本地存储中删除
                        savedPhotos = savedPhotos.filter(photo => photo.url !== url);
                        localStorage.setItem('dreamyPhotos', JSON.stringify(savedPhotos));
                        
                        // 从网格中删除
                        photoItem.remove();
                        checkEmptyState();
                    }, 300);
                }
            });
            
            photoContainer.appendChild(img);
            photoItem.appendChild(photoContainer);
            photoGrid.appendChild(photoItem);
        }
        
        // 检查空状态
        function checkEmptyState() {
            if (photoGrid.children.length === 0) {
                emptyState.classList.remove('hidden');
            } else {
                emptyState.classList.add('hidden');
            }
        }
        
        // 关闭模态框
        closeModal.addEventListener('click', closePhotoModal);
        
        function closePhotoModal() {
            photoModal.classList.remove('active');
            document.body.style.overflow = '';
        }
        
        // 点击模态框外部关闭
        photoModal.addEventListener('click', (e) => {
            if (e.target === photoModal) {
                closePhotoModal();
            }
        });
        
        // 按ESC键关闭模态框
        document.addEventListener('keydown', (e) => {
            if (e.key === 'Escape' && photoModal.classList.contains('active')) {
                closePhotoModal();
            }
        });
        
        // 滚动时导航栏效果
        window.addEventListener('scroll', () => {
            const header = document.querySelector('header');
            if (window.scrollY > 50) {
                header.classList.add('py-2');
                header.classList.remove('py-4');
            } else {
                header.classList.add('py-4');
                header.classList.remove('py-2');
            }
        });
    </script>
</body>
</html>
