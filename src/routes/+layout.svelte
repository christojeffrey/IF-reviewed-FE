<script lang="ts">
	import type { PageData } from './$types';

	import {
		getAuth,
		GoogleAuthProvider,
		signInWithPopup,
		onAuthStateChanged,
		signOut
	} from 'firebase/auth';
	import { initializeApp } from 'firebase/app';
	const firebaseConfig = {
		apiKey: 'AIzaSyAkA7o3sSHUZ46PP0zgRpJtqIkWnTzPHYI',
		authDomain: 'if-reviewed.firebaseapp.com',
		projectId: 'if-reviewed',
		storageBucket: 'if-reviewed.appspot.com',
		messagingSenderId: '118274056714',
		appId: '1:118274056714:web:e4c68ce70a8d42aaa1b5f3',
		measurementId: 'G-5NG1YPLRSE'
	};

	// Initialize Firebase
	const app = initializeApp(firebaseConfig);
	// Initialize Firebase Authentication and get a reference to the service
	const auth = getAuth(app);
	const provider = new GoogleAuthProvider();
	let isLoggedIn: boolean = false;

	onAuthStateChanged(auth, (user) => {
		if (user) {
			// User is signed in, see docs for a list of available properties
			// https://firebase.google.com/docs/reference/js/firebase.User
			const uid = user.uid;
			console.log('user is signed in');
			console.log(uid);
			isLoggedIn = true;
			// ...
		} else {
			// User is signed out
			// ...
			console.log('user is signed out');
			isLoggedIn = false;
		}
	});
	function handleSignOut() {
		signOut(auth)
			.then(() => {
				// Sign-out successful.
				console.log('sign out success');
			})
			.catch((error) => {
				// An error happened.
				console.log('sign out failed');
			});
		isLoggedIn = false;
	}
	function handleLoginButton() {
		signInWithPopup(auth, provider)
			.then((result) => {
				// This gives you a Google Access Token. You can use it to access the Google API.
				const credential = GoogleAuthProvider.credentialFromResult(result);
				const token = credential?.accessToken;
				// The signed-in user info.
				const user = result.user;
				console.log(user);
				// ...
				isLoggedIn = true;
			})
			.catch((error) => {
				// Handle Errors here.
				const errorCode = error.code;
				const errorMessage = error.message;
				// The email of the user's account used.
				const email = error.email;
				// The AuthCredential type that was used.
				const credential = GoogleAuthProvider.credentialFromError(error);
				// ...
			});
	}
</script>

<h1>hello</h1>
{#if isLoggedIn}
	<button on:click={handleSignOut}>sign out</button>
{:else}
	<button on:click={handleLoginButton}>login</button>
{/if}
<slot />
