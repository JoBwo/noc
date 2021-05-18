<div style="overflow: auto; height: 100%;">

    

    {#if selectedUser !== ""}
        <button on:click="{returnToOverview}">&#8592; Zurück zur Übersicht</button>
        <RadiusView user_name={selectedUser}/>
    {:else}
        <div on:click="{e => selectedUser = "test1"}" class="link-mockup">Test1 laden</div>
    {/if}

</div>

<script>
    import RadiusView from './RadiusView.svelte';

    let selectedUser = "";

    function returnToOverview(){
        selectedUser = "";
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