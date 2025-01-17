<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Campo Conecta</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600;700&display=swap');

        body {
            margin: 0;
            font-family: 'Poppins', sans-serif;
            background: linear-gradient(135deg, #0066cc, #003366);
            color: white;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            background: #ffffff;
            border-radius: 20px;
            box-shadow: 0 8px 30px rgba(0, 0, 0, 0.3);
            padding: 40px;
            width: 100%;
            max-width: 700px;
            color: #333;
        }

        h1 {
            font-size: 32px;
            color: #0066cc;
            margin-bottom: 25px;
            text-align: center;
        }

        h2 {
            font-size: 24px;
            color: #333;
            margin-bottom: 20px;
        }

        label {
            display: block;
            text-align: left;
            margin-bottom: 5px;
            font-weight: 600;
            color: #0066cc;
        }

        input, select, textarea {
            width: 100%;
            padding: 14px;
            margin-bottom: 20px;
            border: 1px solid #ccc;
            border-radius: 10px;
            font-size: 16px;
            box-shadow: inset 0 2px 6px rgba(0, 0, 0, 0.1);
        }

        button {
            background-color: #ff6600;
            border: none;
            border-radius: 10px;
            color: white;
            padding: 12px 20px;
            font-size: 18px;
            font-weight: 600;
            cursor: pointer;
            width: 100%;
            transition: all 0.3s ease;
        }

        button:hover {
            background-color: #e65a00;
            transform: scale(1.02);
        }

        .hidden {
            display: none;
        }

        ul {
            list-style-type: none;
            padding: 0;
        }

        li {
            background: #f9f9f9;
            border: 1px solid #ddd;
            border-radius: 12px;
            margin: 10px 0;
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
        }

        .post-section {
            margin-bottom: 40px;
        }

        .profile-button {
            background-color: #0066cc;
            border: none;
            border-radius: 10px;
            color: white;
            padding: 10px 20px;
            font-size: 18px;
            font-weight: 600;
            cursor: pointer;
            width: auto;
            transition: all 0.3s ease;
        }

        .profile-button:hover {
            background-color: #004da3;
            transform: scale(1.05);
        }

        .post-content {
            font-size: 16px;
            line-height: 1.6;
        }

        .post-author {
            font-size: 14px;
            color: #555;
        }

        .timestamp {
            font-size: 12px;
            color: #777;
        }

        .post-meta {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        a {
            color: #0066cc;
            text-decoration: none;
        }

        a:hover {
            text-decoration: underline;
        }
    </style>
</head>
<body>
<div class="container" id="authContainer">
    <h1>Campo Conecta</h1>
    <form id="loginForm">
        <h2>Login</h2>
        <label for="emailLogin">Email:</label>
        <input type="email" id="emailLogin" placeholder="Digite seu email" required>
        <label for="passwordLogin">Senha:</label>
        <input type="password" id="passwordLogin" placeholder="Digite sua senha" required>
        <button type="button" id="loginButton">Entrar</button>
    </form>

    <form id="registerForm" class="hidden">
        <h2>Registrar-se</h2>
        <label for="userType">Eu sou:</label>
        <select id="userType">
            <option value="produtor">Produtor</option>
            <option value="comprador">Comprador</option>
        </select>
        <label for="emailRegister">Email:</label>
        <input type="email" id="emailRegister" placeholder="Digite seu email" required>
        <label for="passwordRegister">Senha:</label>
        <input type="password" id="passwordRegister" placeholder="Digite sua senha" required>
        <button type="button" id="registerButton">Registrar</button>
    </form>

    <p id="toggleAuth">Ainda não tem uma conta? <a href="#" onclick="toggleForms()">Registre-se aqui</a></p>
</div>

<div class="container hidden" id="feedContainer">
    <h1>Bem-vindo ao Feed</h1>
    <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
        <button class="profile-button" id="profileButton">Meu Perfil</button>
        <button class="profile-button" onclick="logout()">Sair</button>
    </div>
    <p id="feedTitle"></p>

    <div class="post-section">
        <h3>Criar uma postagem:</h3>
        <textarea id="postContent" placeholder="Digite o que deseja postar..."></textarea>
        <button id="postButton">Postar</button>
    </div>

    <h3>Postagens:</h3>
    <ul id="postList"></ul>
</div>
<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-app.js";
    import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword, signOut } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-auth.js";
    import { getFirestore, collection, doc, setDoc, getDoc, getDocs, query, serverTimestamp, updateDoc, arrayUnion } from "https://www.gstatic.com/firebasejs/11.1.0/firebase-firestore.js";

    const firebaseConfig = {
        apiKey: "AIzaSyA6MH4IiYrFBx4UtT0ijXcx5jqCwybSNlM",
        authDomain: "campo-conecta-792a6.firebaseapp.com",
        projectId: "campo-conecta-792a6",
        storageBucket: "campo-conecta-792a6.appspot.com",
        messagingSenderId: "722459678882",
        appId: "1:722459678882:web:a5293441f51a10c764dffb",
        measurementId: "G-CNWKCNB64Y"
    };

    const app = initializeApp(firebaseConfig);
    const auth = getAuth(app);
    const db = getFirestore(app);

    // Função para carregar postagens
    async function loadPosts() {
        const postList = document.getElementById('postList');
        postList.innerHTML = '';

        try {
            const querySnapshot = await getDocs(collection(db, "posts"));
            querySnapshot.forEach((doc) => {
                const post = doc.data();
                const li = document.createElement('li');

                const contentDiv = document.createElement('div');
                contentDiv.className = 'post-content';
                contentDiv.textContent = post.content;

                const metaDiv = document.createElement('div');
                metaDiv.className = 'post-meta';
                metaDiv.innerHTML = `<span class='post-author'>Por: ${post.uid}</span> <span class='timestamp'>${new Date(post.timestamp?.toDate()).toLocaleString()}</span>`;

                const interestButton = document.createElement('button');
                interestButton.textContent = 'Demonstrar Interesse';
                interestButton.className = 'interest-button';
                interestButton.addEventListener('click', () => declareInterest(doc.id, post.uid));

                li.appendChild(contentDiv);
                li.appendChild(metaDiv);
                li.appendChild(interestButton);

                postList.appendChild(li);
            });
        } catch (error) {
            alert('Erro ao carregar postagens: ' + error.message);
        }
    }

    // Função para declarar interesse
    async function declareInterest(postId, postOwnerId) {
        const user = auth.currentUser;
        if (!user) {
            alert('Você precisa estar logado para demonstrar interesse.');
            return;
        }

        if (!postId || !postOwnerId) {
            alert('Informações do post ou do autor estão incompletas.');
            return;
        }

        try {
            const postRef = doc(db, "posts", postId);
            const postSnapshot = await getDoc(postRef);
            if (!postSnapshot.exists()) {
                alert('Postagem não encontrada.');
                return;
            }

            await updateDoc(postRef, {
                interestedUsers: arrayUnion(user.uid)
            });

            const notificationRef = doc(db, "users", postOwnerId);
            await updateDoc(notificationRef, {
                notifications: arrayUnion({
                    type: 'interest',
                    from: user.uid,
                    postId: postId,
                    timestamp: serverTimestamp()
                })
            });

            alert('Interesse declarado com sucesso!');
        } catch (error) {
            alert('Erro ao declarar interesse: ' + error.message);
        }
    }

    // Função de postagem
    document.getElementById('postButton').addEventListener('click', async () => {
        const postContent = document.getElementById('postContent').value;
        const user = auth.currentUser;

        if (!postContent.trim()) {
            alert('O conteúdo da postagem não pode estar vazio.');
            return;
        }

        try {
            const postRef = doc(collection(db, "posts"));
            await setDoc(postRef, {
                uid: user.uid,
                content: postContent,
                timestamp: serverTimestamp(),
                interestedUsers: []
            });

            alert('Postagem criada com sucesso!');
            document.getElementById('postContent').value = '';
            loadPosts();
        } catch (error) {
            alert('Erro ao criar postagem: ' + error.message);
        }
    });

    // Função para mostrar o feed
    async function showFeed(userType) {
        document.getElementById('authContainer').classList.add('hidden');
        document.getElementById('feedContainer').classList.remove('hidden');

        const feedTitle = document.getElementById('feedTitle');
        const oppositeType = userType === "produtor" ? "comprador" : "produtor";
        feedTitle.textContent = `Recomendações de ${oppositeType}s:`;

        await loadPosts();
    }

    // Função de login
    document.getElementById('loginButton').addEventListener('click', async () => {
        const email = document.getElementById('emailLogin').value;
        const password = document.getElementById('passwordLogin').value;

        try {
            const userCredential = await signInWithEmailAndPassword(auth, email, password);
            const user = userCredential.user;

            const userDoc = await getDoc(doc(db, "users", user.uid));
            if (userDoc.exists()) {
                const userType = userDoc.data().userType;
                showFeed(userType);
            } else {
                throw new Error("Usuário não encontrado no Firestore.");
            }
        } catch (error) {
            alert('Erro ao fazer login: ' + error.message);
        }
    });

    // Função de logout
    document.getElementById('logoutButton').addEventListener('click', async () => {
        try {
            await signOut(auth);
            alert('Você saiu!');
            document.getElementById('feedContainer').classList.add('hidden');
            document.getElementById('authContainer').classList.remove('hidden');
        } catch (error) {
            alert('Erro ao sair: ' + error.message);
        }
    });

    // Função de perfil
    document.getElementById('profileButton').addEventListener('click', async () => {
        const user = auth.currentUser;
        if (!user) {
            alert('Usuário não autenticado.');
            return;
        }

        const userDoc = await getDoc(doc(db, "users", user.uid));
        if (userDoc.exists()) {
            const userData = userDoc.data();
            const notifications = userData.notifications || [];

            let profileContent = `Perfil:\nEmail: ${userData.email}\nTipo: ${userData.userType}\n\nNotificações:\n`;
            notifications.forEach((notif, index) => {
                profileContent += `${index + 1}. Interesse no post ${notif.postId} por ${notif.from}\n`;
            });

            alert(profileContent);
        } else {
            alert('Perfil não encontrado.');
        }
    });

    // Função de alternar entre login e registro
    window.toggleForms = function toggleForms() {
        const loginForm = document.getElementById('loginForm');
        const registerForm = document.getElementById('registerForm');
        const toggleLink = document.getElementById('toggleAuth');

        if (loginForm.classList.contains('hidden')) {
            loginForm.classList.remove('hidden');
            registerForm.classList.add('hidden');
            toggleLink.innerHTML = "Ainda não tem uma conta? <a href='#'>Registre-se aqui</a>";
        } else {
            loginForm.classList.add('hidden');
            registerForm.classList.remove('hidden');
            toggleLink.innerHTML = "Já tem uma conta? <a href='#'>Faça login aqui</a>";
        }
    };

    // Função para o botão de registro
    document.getElementById('registerButton').addEventListener('click', async () => {
        const email = document.getElementById('emailRegister').value;
        const password = document.getElementById('passwordRegister').value;
        const userType = document.getElementById('userType').value;

        if (!email || !password) {
            alert('Por favor, preencha todos os campos.');
            return;
        }

        try {
            const userCredential = await createUserWithEmailAndPassword(auth, email, password);
            const user = userCredential.user;

            // Registra o usuário no Firestore
            await setDoc(doc(db, "users", user.uid), {
                email: email,
                userType: userType,
                notifications: [] // Inicializa o array de notificações
            });

            alert('Conta criada com sucesso!');
            showFeed(userType);
        } catch (error) {
            alert('Erro ao registrar usuário: ' + error.message);
        }
    });
</script>
</body>
</html>
