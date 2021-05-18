<div style="overflow: auto; height: 100%;">
    <h2>Radius Account {user_name}</h2>
    <table class="user-info">
        <tr>
            <td>Benutzername:</td>
            <td>{user_name}</td>
        </tr>
        <tr>
            <td>Cleartext-Password:</td>
            <td>{user_cleartext_password}</td>
        </tr>
        <tr>
            <td>Mac-Adresse:</td>
            <td>{user_mac}</td>
        </tr>
        <tr>
            <td>Status:</td>
            <td>
                {user_blocked ? "Inaktiv" : "Aktiv"}
                <span style="padding-left: 10px;">
                    {#if user_blocked}
                        <button class="noc-button" on:click={activateUser}>Aktivieren</button>
                    {:else}
                        <button class="noc-button" on:click={deactivateUser}>Deaktivieren</button>
                    {/if}
                </span>
            </td>
        </tr>
    </table>

    <br><br>
    <div class="right-button">
        <button on:click={updateAll}>Daten aktualisieren</button>
    </div>

    <table class="attr-table">
        <tr>
            <th colspan="4" style="text-align: left;">CHECK-Items</th>
        </tr>
        <tr><th>ID</th><th>ATTRIBUTE</th><th>OP</th><th>VALUE</th></tr>
        {#each check_items as check}
            <tr>
                <td>{check.id}</td>
                <td>{check.attribute}</td>
                <td>{check.op}</td>
                <td>{check.value}</td>
            </tr>
        {/each}
    </table>

    <br><br>

    <table class="attr-table">
        <tr>
            <th colspan="4" style="text-align: left;">REPLY-Items</th>
        </tr>
        <tr><th>ID</th><th>ATTRIBUTE</th><th>OP</th><th>VALUE</th></tr>
        {#each reply_items as reply}
            <tr>
                <td>{reply.id}</td>
                <td>{reply.attribute}</td>
                <td>{reply.op}</td>
                <td>{reply.value}</td>
            </tr>
        {/each}
    </table>
</div>
<script>

    export let user_name = "";
    let user_cleartext_password = "";
    let user_blocked = "";
    let user_mac = "";

    let check_items = [];
    let reply_items = [];

    let test;
    let test2 = [
        {"value": "val"},
        {"value": "val"}
    ];

    if(user_name != ""){
        updateAll();
    }

    function updateAll(){
        updateUserData();
        updateData("check");
        updateData("reply");
    }

    function activateUser(){
        setActivation("activate");
    }

    function deactivateUser(){
        setActivation("deactivate");
    }

    async function setActivation(type){
        var data = {username: user_name, action: type};
        var fd = new FormData();
        for(var i in data){
            fd.append(i,data[i]);
        }
		const res = await fetch('https://testing.inspiration-feuerwehr.de/radius.php', {
			method: 'POST',
            body: fd
		})
		
		const as = await res.json();
        console.log(as['worked']);
        if(as['worked'] == 1){
            if(type == "deactivate"){
                user_blocked = true;
            }else{
                user_blocked = false;
            }
            updateData("check");
        }
    }

    async function updateUserData () {
        var data = {username: user_name, action: 'getinfo'};
        var fd = new FormData();
        for(var i in data){
            fd.append(i,data[i]);
        }
		const res = await fetch('https://testing.inspiration-feuerwehr.de/radius.php', {
			method: 'POST',
            body: fd
		})
		
		const as = await res.json();
		user_name = as['user_name'];
        user_cleartext_password = as['user_cleartext_password'];
        user_blocked = as['user_blocked'] == 0 ? false : true;
        user_mac = as['user_mac'];
	}

    async function updateData(type){
        var data = {username: user_name, action: 'get' + type + 'items'};
        var fd = new FormData();
        for(var i in data){
            fd.append(i,data[i]);
        }
		const res = await fetch('https://testing.inspiration-feuerwehr.de/radius.php', {
			method: 'POST',
            body: fd
		})
		
        if(type == "check"){
            check_items = await res.json();
        }else{
            reply_items = await res.json();
        }
    }

    // JSON Array im Format:
    // [{"id": "10", "attribute": "Cleartext-Password", "op": ":=", "value": "welcome"},{"id": "11", "attribute": "Framed-IP-Address", "op": ":=", "value": "10.172.2.10"}]
</script>

<style>
    table, td, th, tr{
        border: 1px solid gray;
        border-collapse: collapse;
        padding: 10px 14px;
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

    .user-info td{
        width: 200px;
    }

    .attr-table{
        width: calc(100% - 60px);
    }
    
    .right-button {
        width: calc(100% - 40px);
        margin-right: 20px;
    }

    .right-button button{
        float: right;
    }
</style>
