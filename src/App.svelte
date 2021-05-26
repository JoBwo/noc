<div class="topnav">
	<div on:click={() => changePage(0)} class="{page === 0 ? "active noselect" : "noselect"}">{home}</div>
	<div on:click={() => changePage(1)} class="{page === 1 ? "active noselect" : "noselect"}">{radius}</div>
	<div on:click={() => changePage(2)} class="{page === 2 ? "active noselect" : "noselect"}">{contract}</div>
</div>
<div class="headblock"></div>

<div class="inner-app">
	{#if page === 0}
		<img src="https://i.imgur.com/yed5Zfk.gif" alt="Unternehmenswerte">
		<h1>You just got Rick Rolled!</h1>
	{:else if page === 1}
		<Radius baseurl={baseurl}/>
	{:else if page === 2}
		<Contract baseurl={baseurl}/>
	{/if}
</div>

<script>
	import Radius from './Radius.svelte';
	import Contract from './Contract.svelte';

	let page = 1;
	let home = "Startseite";
	let radius = "Radius";
	let contract = "Verträge";

	let baseurl = "https://testing.inspiration-feuerwehr.de/";

	const queryString = window.location.search;
	const urlParams = new URLSearchParams(queryString);

	if(isset(urlParams.get('page'))){
		page = parseInt(urlParams.get('page'));
	}

	function changePage(p){
		page = p;
		const urlParams = new URLSearchParams();
		urlParams.set('page', page);
		window.location.search = urlParams.toString();
	}

	function isset(v){
		return (v !== 'undefined' && v !== null);
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

	.noselect{
        user-select: none;
    }
</style>