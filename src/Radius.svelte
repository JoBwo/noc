<div style="overflow: auto; height: 100%;">

    

    {#if selectedUser !== ""}
        <button on:click="{returnToOverview}">&#8592; Zurück zur Übersicht</button>
        <RadiusView user_name={selectedUser} baseurl={baseurl}/>
    {:else}
        <h2>Radius Accounts</h2>
        <button class="load-button" on:click={queryUsers}>Aktualisieren</button>
        <table class="attr-table">
            <tr>
                <th>ID</th>
                <th>Benutzername</th>
                <th>Status</th>
            </tr>
            {#each users as user}
            <tr>
                <td>{user.id}</td>
                <td on:click={() => loadUser(user.name)} class="mock-link">{user.name}</td>
                <td>{user.status}</td>
            </tr>
            {/each}
        </table>
    {/if}

</div>

<script>
    import RadiusView from './RadiusView.svelte';

    let selectedUser = "";

    export let baseurl = "";

    document.title = "Radius Accounts";

    const queryString = window.location.search;
	const urlParams = new URLSearchParams(queryString);

    if(isset(urlParams.get('user'))){
		selectedUser = urlParams.get('user');
	}

    let users = [];

    queryUsers();

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
            urlParams.delete('rpage');
        }else {
            urlParams.set('user', selectedUser);
        }
		window.location.search = urlParams.toString();
    }

    async function queryUsers(){
        var data = {username: '', action: 'getall'};
        var fd = new FormData();
        for(var i in data){
            fd.append(i,data[i]);
        }
		const res = await fetch(baseurl + 'radius.php', {
			method: 'POST',
            body: fd
		})
		
        users = await res.json();
    }

    function isset(v){
		return (v !== 'undefined' && v !== null);
	}
</script>

<style>
    table, td, th, tr{
        border: 2px solid lightgray;
        border-collapse: collapse;
        padding: 10px 14px;

        width: calc(100% - 40px);
    }

    table{
        margin-left: 20px;
    }

    .attr-table td, th, tr{
        border: 1px solid white;
        border-collapse: collapse;
        padding: 10px 14px;
    }

    .attr-table tr:nth-child(odd) {
        background-color: var(--bright-green);
    }

    .attr-table tr:hover{
        background-color: var(--light-green);
    }


    .attr-table th, td{
        text-align: left;
        vertical-align: middle;
        padding: 10px;
        border-bottom: 1px solid #ddd;
    }

    .attr-table th {
        background-color: var(--middle-green);
        color: white;
    }

    .mock-link:hover{
        color: var(--middle-green);
    }

    .mock-link:hover{
        color: var(--dark-green);
        text-decoration: underline;
    }

    .load-button{
        margin-right: 20px;
        float: right;
    }

</style>