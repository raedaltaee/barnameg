رائد الطائي, [05/07/2025 06:09 م]
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Firebase Auth Fix</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        body { background: #e3ecf7; font-family: Arial, sans-serif; }
        #messageBox { display: none; padding: 10px; margin: 10px 0; border: 1px solid; border-radius: 5px; }
        #authCard { background: #fff; margin: 40px auto; padding: 30px; border-radius: 10px; width: 350px; box-shadow: 0 0 10px #bbb; text-align: center; }
    </style>
</head>
<body>
    <div id="authCard">
        <h2>Firebase Authentication</h2>
        <div id="messageBox"></div>
        <div id="authStatus">Initializing Firebase...</div>
        <div>Your UID: <span id="userIdDisplay">Loading...</span></div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, doc, setDoc, onSnapshot } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // إعدادات Firebase (استبدل القيم بقيم مشروعك)
        const firebaseConfig = {
            apiKey: "ضع هنا apiKey",
            authDomain: "ضع هنا authDomain",
            projectId: "ضع هنا projectId",
            storageBucket: "ضع هنا storageBucket",
            messagingSenderId: "ضع هنا messagingSenderId",
            appId: "ضع هنا appId"
        };
        const appId = firebaseConfig.appId;

        let app, auth, db, userId;

        const messageBox = document.getElementById('messageBox');
        const authStatusElement = document.getElementById('authStatus');
        const userIdDisplayElement = document.getElementById('userIdDisplay');

        function showMessage(message, isError = false) {
            messageBox.textContent = message;
            messageBox.style.display = 'block';
            if (isError) {
                messageBox.style.backgroundColor = '#ffcdd2';
                messageBox.style.color = '#b71c1c';
                messageBox.style.borderColor = '#ef5350';
            } else {
                messageBox.style.backgroundColor = '#e8f5e9';
                messageBox.style.color = '#2e7d32';
                messageBox.style.borderColor = '#66bb6a';
            }
        }

        window.onload = async function() {
            try {
                app = initializeApp(firebaseConfig);
                auth = getAuth(app);
                db = getFirestore(app);

                authStatusElement.textContent = "Authenticating...";

                onAuthStateChanged(auth, async (user) => {
                    if (user) {
                        userId = user.uid;
                        authStatusElement.textContent = Authenticated as: ${userId};
                        userIdDisplayElement.textContent = userId;
                        showMessage("Authentication successful!");

                        const usersCollectionRef = collection(db, artifacts/${appId}/users/${userId}/profile);
                        const userDocRef = doc(usersCollectionRef, "data");

                        await setDoc(userDocRef, {
                            lastLogin: new Date().toISOString(),
                            appId: appId
                        }, { merge: true });

                        onSnapshot(userDocRef, (docSnap) => {
                            if (docSnap.exists()) {
                                // يمكنك تحديث الواجهة هنا حسب بيانات المستخدم
                            }
                        }, (error) => {
                            showMessage(Error listening to data: ${error.message}, true);
                        });

رائد الطائي, [05/07/2025 06:09 م]
} else {
                        userId = 'not-authenticated';
                        authStatusElement.textContent = "Not authenticated. Attempting sign-in...";
                        userIdDisplayElement.textContent = "N/A";
                        try {
                            await signInAnonymously(auth);
                        } catch (error) {
                            showMessage(Login failed: ${error.message}. Please ensure authentication methods are enabled in Firebase console., true);
                            authStatusElement.textContent = "Authentication Failed.";
                            userIdDisplayElement.textContent = "Error";
                        }
                    }
                });

            } catch (error) {
                showMessage(Firebase initialization error: ${error.message}, true);
            }
        };
    </script>
</body>
</html># barnameg
