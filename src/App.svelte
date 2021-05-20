<div class="topnav">
	<div on:click={setHomePage} class="{page === 0 ? "active" : ""}">{home}</div>
	<div on:click={setRadiusPage} class="{page === 1 ? "active" : ""}">{radius}</div>
</div>
<div class="headblock"></div>

<div class="inner-app">
	{#if page === 0}
		<p>{home}</p>
	{:else if page === 1}
		<Radius selectedUser={selectedUser}/>
	{/if}
</div>

<script>
	import Radius from './Radius.svelte';

	let page = 0;
	let home = "Startseite";
	let radius = "Radius";
	let selectedUser = "";

	const queryString = window.location.search;
	const urlParams = new URLSearchParams(queryString);

	if(isset(urlParams.get('page'))){
		page = parseInt(urlParams.get('page'));
	}
	if(isset(urlParams.get('user'))){
		let user = urlParams.get('user');
		if(user !== null){
			selectedUser = user;
		}
	}

	function setHomePage(){
		changePage(0);
	}

	function setRadiusPage(){
		changePage(1);
	}
	

	function changePage(p){
		page = p;
		const urlParams = new URLSearchParams();
		urlParams.set('page', page);
		window.location.search = urlParams.toString();
	}

	function isset(v){
		return (v !== 'undefined');
	}

	
</script>

<style>
	:root{
	--dark-green: #007C46;
	--middle-green: #009265;
	--light-green: #9AC6AD;
	--bright-green: #CEE2D6;
	--dark-gray: #444;
	}

	.topnav {
		background-color: var(--dark-gray);
		
		overflow: hidden;

		position: fixed;
		top: 0px;
		left: 0px;
		width: 100%;
	}

	.topnav div{
		float: left;
		color: #f2f2f2;
		text-align: center;
		padding: 14px 16px;
		text-decoration: none;
		font-size: 20px;
	}

	.topnav .active {
		background-color: var(--middle-green);
	}

	.topnav div:not(.active):hover{
		background-color: var(--dark-green);
	}

	.headblock {
		height: 60px;
	}

	.inner-app {
		width: calc(100% - 40px);
		height: calc(100% - 100px);
		border: 1px solid gray;
		padding: 10px;
		position: fixed;
	}
</style>