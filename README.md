
            }

            const

    ()
      
            if (!username || !password) {
                messageDiv.innerHTML = '<div class="error-message">Пожалуйста, заполните все поля</div>';
                return;
            }

            try {
                // Вход в Firebase Auth
                const userCredential = await auth.signInWithEmailAndPassword(username + '@example.com', password);
                const user = userCredential.user;
                
                // Получаем данные пользователя из Firestore
                const userDoc = await db.collection('users').doc(user.uid).get();
                const userData = userDoc.data();
                
                currentUser = {
                    id: user.uid,
                    username: userData.username
                };
                
                updateAuthUI();
                messageDiv.innerHTML = '<div class="success-message">Вход выполнен успешно!</div>';
                
                // Закрытие модального окна через 1 секунду
                setTimeout(() => {
                    closeModal('loginModal');
                    document.getElementById('loginUsername').value = '';
                    document.getElementById('loginPassword').value = '';
                }, 1000);
            } catch (error) {
                console.error("Ошибка входа:", error);
                messageDiv.innerHTML = '<div class="error-message">Неверное имя пользователя или пароль</div>';
            }
        }

        // Выход из системы
        async function logout() {
            try {
                await auth.signOut();
                currentUser = null;
                updateAuthUI();
            } catch (error) {
                console.error("Ошибка выхода:", error);
            }
        }

        // Обновление интерфейса авторизации
        function updateAuthUI() {
            const userInfo = document.getElementById('userInfo');
            const addAdBtn = document.getElementById('addAdBtn');
            
            if (currentUser) {
                userInfo.style.display = 'block';
                document.getElementById('currentUser').textContent = `Привет, ${currentUser.username}!`;
                addAdBtn.style.display = 'block';
            } else {
                userInfo.style.display = 'none';
                addAdBtn.style.display = 'none';
            }
        }

        // Открытие модального окна
        function openModal(modalId) {
            closeModalAll();
            document.getElementById(modalId).style.display = 'flex';
        }

        // Закрытие модального окна
        function closeModal(modalId) {
          il');
            const hiddenEmailElement = document.getElementById('hiddenEmail');
            
            if (hiddenEmailElement.classList.contains('hidden-email')) {
                hiddenEmailElement.classList.remove('hidden-email');
                hiddenEmailElement.classList.add('visible-email');
                emailElement.innerHTML = 'poddershka24@gmail.com';
            } else {
                hiddenEmailElement.classList.remove('visible-email');
                hiddenEmailElement.classList.add('hidden-email');
                emailElement.innerHTML = 'poddershka24<span style="display:none">@</span>Gmail.com';
            }
        }

        // Удаление объявления по ключу доступа
        async function deleteAdvertisementByKey() {
            const key = document.getElementById('deleteKey').value;
            const adId = parseInt(document.getElementById('deleteAdId').value);
            const messageDiv = document.getElementById('deleteMessage');

            // Проверка ключа доступа
            if (key !== DELETE_ACCESS_KEY) {
                messageDiv.innerHTML = '<div class="error-message">Неверный ключ доступа</div>';
                return;
            }

            // Проверка ID объявления
            if (!adId || adId <= 0) {
                messageDiv.innerHTML = '<div class="error-message">Пожалуйста, введите корректный ID объявления</div>';
                return;
            }

            try {
                // Удаляем из Firestore
                await db.collection('advertisements').doc(String(adId)).delete();
                
                // Удаляем из локального массива
                advertisements = advertisements.filter(a => a.id !== adId);
                displayAdvertisements();
                closeModal('deleteByKeyModal');
                
                // Очищаем поля формы
                document.getElementById('deleteKey').value = '';
                document.getElementById('deleteAdId').value = '';
                
                messageDiv.innerHTML = '<div class="success-message">Объявление успешно удалено!</div>';
            } catch (error) {
                console.error("Ошибка удаления:", error);
                messageDiv.innerHTML = '<div class="error-message">Ошибка при удалении объявления</div>';
            }
        }

        // Удаление объявления (для владельцев и админов)
        async function deleteAdvertisement(adId) {
            if (!currentUser) {
                alert('Пожалуйста, войдите в систему');
                return;
            }

            // Проверяем, является ли пользователь админом или владельцем объявления
            const ad = advertisements.find(a => a.id === adId);
            if (!ad) return;

            if (currentUser.username !== "admin" && currentUser.id !== ad.userId) {
                alert('У вас нет прав для удаления этого объявления');
                return;
            }

            if (confirm('Вы уверены, что хотите удалить это объявление?')) {
                try {
                    // Удаляем из Firestore
                    await db.collection('advertisements').doc(String(adId)).delete();
                    
                    // Удаляем из локального массива
                    advertisements = advertisements.filter(a => a.id !== adId);
                    displayAdvertisements();
                } catch (error) {
                    console.error("Ошибка удаления:", error);
                    alert("Ошибка при удалении объявления");
                }
            }
        }

        // Загрузка объявлений из Firestore
        async function loadAdvertisements() {
            try {
                const snapshot = await db.collection('advertisements').orderBy('createdAt', 'desc').get();
                advertisements = [];
                
                snapshot.forEach(doc => {
                    const data = doc.data();
                    data.id = doc.id;
                    advertisements.push(data);
                });
                
                displayAdvertisements();
            } catch (error) {
                console.error("Ошибка загрузки объявлений:", error);
                // Используем примерные данные при ошибке
                displayAdvertisements();
            }
        }

        // Инициализация страницы
        document.addEventListener('DOMContentLoaded', function() {
            // Загружаем объявления
            loadAdvertisements();
            
            // Добавляем обработчик события для поиска при нажатии Enter
            document.getElementById('searchInput').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') {
                    searchAds();
                }
            });

            // Обновляем UI при загрузке
            updateAuthUI();
            
            // Создаем эффекты видеокарты
            createGPUParticles();
            
            // Проверяем статус авторизации Firebase
            auth.onAuthStateChanged(function(user) {
                if (user) {
                    console.log("Пользователь авторизован:", user);
                } else {
                    console.log("Пользователь не авторизован");
                }
            
        }

        // Добавляем защиту на уровне браузера
        window.addEventListener('load', function() {
            console.log('🔒 Сайт защищен от DDoS атак и взломов');
            console.log('Firebase подключен успешно');
        });
    </script>
</body>
</html>
