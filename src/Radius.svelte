<div style="overflow: auto; height: 100%;">

    

    {#if selectedUser !== ""}
        <button on:click="{returnToOverview}">&#8592; Zurück zur Übersicht</button>
        <RadiusView user_name={selectedUser}/>
    {:else}
        <div on:click="{() => loadUser("test1")}" class="link-mockup">Test1 laden</div>
    {/if}

</div>

<script>
    import RadiusView from './RadiusView.svelte';

    export let selectedUser = "";


    function returnToOverview(){
        selectedUser = "";
        updateUser();
    }

    function loadUser(us){
        selectedUser = us;
        updateUser();
    }
    
    function updateUser() {
		const search = window.location.search;
		const urlParams = new URLSearchParams(search);
		if(selectedUser === ""){
            urlParams.delete('user');
        }else {
            urlParams.set('user', selectedUser);
        }
		window.location.search = urlParams.toString();
    }
</script>

<style>
    .link-mockup{
        text-decoration: underline;
        color: var(--dark-green);
    }

    .link-mockup:hover{
        color: var(--middle-green);
    }


</style>