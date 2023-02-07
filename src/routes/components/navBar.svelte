<script lang="ts">
	import { goto } from '$app/navigation';
	import SearchBar from './searchBar.svelte';
	import {
		getAuth,
		GoogleAuthProvider,
		signInWithPopup,
		onAuthStateChanged,
		signOut,
		getIdToken
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
	import HMIFLogo from '../../assets/HMIF-Logo.png';

	// Initialize Firebase
	const app = initializeApp(firebaseConfig);
	// Initialize Firebase Authentication and get a reference to the service
	const auth = getAuth(app);
	const provider = new GoogleAuthProvider();

	let isLoggedIn: boolean = false;
	let userData: any;
	let isUserModalOpen: boolean = false;

	function handleImageClick() {
		isUserModalOpen = !isUserModalOpen;
	}
	onAuthStateChanged(auth, (user) => {
		if (user) {
			// User is signed in, see docs for a list of available properties
			const uid = user.uid;
			console.log('user is signed in');
			console.log(uid);
			isLoggedIn = true;
			// set user to local storage
			if (typeof localStorage !== 'undefined') {
				localStorage.setItem('uid', uid);
			}
			// get id token
			auth.currentUser
				?.getIdToken(true)
				.then(function (idToken) {
					// Send token to your backend via HTTPS
					// ...
					if (typeof localStorage !== 'undefined') {
						localStorage.setItem('idToken', idToken);
					}
				})
				.catch(function (error) {
					// Handle error
					console.log(error);
				});
			userData = user;
		} else {
			// User is signed out
			// ...
			console.log('user is signed out');
			isLoggedIn = false;
			// remove user from local storage
			//    prevent localStorage not defined error
			if (typeof localStorage !== 'undefined') {
				localStorage.removeItem('uid');
				localStorage.removeItem('idToken');
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
					localStorage.removeItem('uid');
					localStorage.removeItem('idToken');
				}
			})
			.catch((error) => {
				// An error happened.
				console.log('sign out failed');
			});
		isUserModalOpen = false;
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
				// set user to local storage
				if (typeof localStorage !== 'undefined') {
					localStorage.setItem('uid', user.uid);
				}
				// get id token
				auth.currentUser
					?.getIdToken(true)
					.then(function (idToken) {
						// Send token to your backend via HTTPS
						// ...
						if (typeof localStorage !== 'undefined') {
							localStorage.setItem('idToken', idToken);
						}
					})
					.catch(function (error) {
						// Handle error
						console.log('error');
					});
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
	function handleBackgroundClick() {
		isUserModalOpen = false;
	}
</script>

<div class="navbar-container">
	<div class="wrapper">
		<div class="image-container">
			<button on:click={() => goto('/')}>
				<img class="home-button" src={HMIFLogo} alt="Home" />
			</button>
		</div>
		<SearchBar bind:searchQuery onSearch={handleSearch} />
		{#if isLoggedIn}
			<div class="image-container">
				<button on:click={handleImageClick}>
					<img class="user-profile" src={userData.photoURL} alt="user profile" />
				</button>
				{#if isUserModalOpen}
					<div class="card"><button on:click={handleSignOut}>sign out</button></div>
				{/if}
			</div>
		{:else}
			<button on:click={handleLoginButton}>login</button>
		{/if}
		<!-- modal if isUserModalOpen -->
		{#if isUserModalOpen}
			<button class="background" on:click={handleBackgroundClick} />
		{/if}
	</div>
</div>

<style>
	.image-container {
		position: relative;
	}
	.card {
		position: absolute;
		top: 50px;
		right: 0;
		background-color: white;
		border-radius: 8px;
		padding: 8px;
		z-index: 2;
	}
	button {
		border: none;
	}

	.background {
		/* open in the top right, make whole scren darker */
		position: absolute;
		top: 0;
		right: 0;
		bottom: 0;
		left: 0;
		background-color: rgba(0, 0, 0, 0.1);
		z-index: 1;
		border: none;
	}

	.navbar-container {
		display: flex;
		width: 100%;
	}
	.wrapper {
		display: flex;
		padding: 8px;
		flex-direction: row;
		align-items: center;
		justify-content: space-between;
		width: 100%;
	}

	.user-profile {
		border-radius: 50%;
		width: 40px;
	}

	.home-button {
		width: 45px;
	}
	.home-button:hover {
		cursor: pointer;
	}
</style>
