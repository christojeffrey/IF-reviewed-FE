<script lang="ts">
	import { goto } from '$app/navigation';
	import SearchBar from './searchBar.svelte';
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
	let userData: any;

	onAuthStateChanged(auth, (user) => {
		if (user) {
			// User is signed in, see docs for a list of available properties
			const uid = user.uid;
			console.log('user is signed in');
			console.log(uid);
			isLoggedIn = true;
			// set user to local storage
			if (typeof localStorage !== 'undefined') {
				localStorage.setItem('user', JSON.stringify(user));
			}
			userData = user;
		} else {
			// User is signed out
			// ...
			console.log('user is signed out');
			isLoggedIn = false;
			// remove user from local storage
			//    prevent localStorage not defined error
			if (typeof localStorage !== 'undefined') {
				localStorage.removeItem('user');
			}
		}
	});
	function handleSignOut() {
		signOut(auth)
			.then(() => {
				// Sign-out successful.
				console.log('sign out success');
				// remove user from local storage
				if (typeof localStorage !== 'undefined') {
					localStorage.removeItem('user');
				}
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
	let searchQuery: any;
	async function handleSearch(e: any) {
		e.preventDefault();

		goto(`/?searchQuery=${searchQuery}`);
	}
</script>

<div class="navbar-container">
	<div class="wrapper">
		<SearchBar bind:searchQuery onSearch={handleSearch} />
		{#if isLoggedIn}
			<span>{userData.displayName}</span>
			<img class="user-profile" src={userData.photoURL} alt="user profile picture" />
			<button on:click={handleSignOut}>sign out</button>
		{:else}
			<button on:click={handleLoginButton}>login</button>
		{/if}
	</div>
</div>

<style>
	.navbar-container {
		display: flex;
		width: 100%;
	}
	.wrapper {
		display: flex;
		padding: 10px;
		flex-direction: row;
		align-items: center;
		justify-content: space-between;
		width: 100%;
	}

	.user-profile {
		border-radius: 50%;
		width: 40px;
	}
</style>
