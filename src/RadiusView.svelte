<div style="overflow: auto; height: 100%;">
    <h2>Radius Account {user_name}</h2>
    {#if rpage === 0}
        <div class="head-info">
            <div class="user-table">
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
            </div>
            <div class="einwahl">
                <div>
                    <p>
                        Aktuelle Einwahl:
                        {#if user_logon['readable'] == "0"}
                            <span style="color: red;">{user_logon['logon']}</span>
                        {:else}
                            <span style="color: var(--middle-green);">{user_logon['logon']}, {user_logon['readable']}</span>
                        {/if}
                    </p>
                </div>
                <div>
                    <p>
                        Letzte Einwahl:
                        {#if user_last_logon['logon'] == "Vorherige Einwahl"}
                            {user_last_logon['readable']}
                        {:else}
                            {user_last_logon['logon']}
                        {/if}
                    </p>
                </div>
            </div>
        </div>

        <br><br>
        <div class="right-button">
            <button on:click={updateAll}>Daten aktualisieren</button>
        </div>

        <table class="attr-table">
            <tr>
                <th colspan="5" style="text-align: left;">CHECK-Items</th>
            </tr>
            <tr><th>ID</th><th>ATTRIBUTE</th><th>OP</th><th>VALUE</th><th></th></tr>
            {#each check_items as check}
                <tr>
                    <td>{check.id}</td>
                    <td>{check.attribute}</td>
                    <td>{check.op}</td>
                    <td>{check.value}</td>
                    <td>
                        {#if check.del == "true"}
                            <button class="del-button" on:click={() => removeEntry("radcheck", check.id)}>&#10006;</button>
                        {/if}
                    </td>
                </tr>
            {/each}
        </table>

        <br><br>

        <table class="attr-table">
            <tr>
                <th colspan="5" style="text-align: left;">REPLY-Items</th>
            </tr>
            <tr><th>ID</th><th>ATTRIBUTE</th><th>OP</th><th>VALUE</th><th></th></tr>
            {#each reply_items as reply}
                <tr>
                    <td>{reply.id}</td>
                    <td>{reply.attribute}</td>
                    <td>{reply.op}</td>
                    <td>{reply.value}</td>
                    <td>
                        {#if reply.del == "true"}
                            <button class="del-button" on:click={() => removeEntry("radreply", reply.id)}>&#10006;</button>
                        {/if}
                    </td>
                </tr>
            {/each}
        </table>

        <div class="addbutton">
            <button on:click={e => rpage = 1}>Eintrag hinzufügen</button>
        </div>
    {:else if rpage === 1}
        <h3>Neuen Wert speichern</h3><br>
        Tabelle:
        <select bind:value={new_table} required>
            <option value="radreply">Reply</option>
            <option value="radcheck">Check</option>
        </select><br>
        Attribut:
        <select bind:value={new_attribute} required>
            <option value="MAC">MAC-Adresse</option>
            <option value="Framed-IP-Adresse">Framed-IP-Adresse</option>
        </select><br>
        Operator:
        <select bind:value={new_operator} required>
            <option value="=">=</option>
            <option value=":=">:=</option>
        </select><br>
        Wert:
        <input type="text" bind:value={new_value}><br>
        <button on:click={saveNewEntry}>Speichern</button>
        <button on:click={e => rpage = 0}>Abbrechen</button>
    {/if}
</div>
<script>
    export let user_name = "";
    let user_cleartext_password = "";
    let user_blocked = "";
    let user_mac = "";

    let check_items = [];
    let reply_items = [];

    let user_logon = {'logon': "", 'readable': ""};
    let user_last_logon = {'logon': "", 'readable': ""};

    let rpage = 0;

    let new_table;
    let new_attribute;
    let new_operator;
    let new_value;

    if(user_name != ""){
        updateAll();
    }

    const search = window.location.search;
	const urlParams = new URLSearchParams(search);
    if(isset(urlParams.get("rpage"))){
        rpage = parseInt(urlParams.get("rpage"));
    }

    function isset(v){
		return (typeof v !== "undefined" && v !== null);
	}

    async function removeEntry(t, i){
        var data = {username: user_name, action: "removedata", table: t, id: i};
        var fd = new FormData();
        for(var i in data){
            fd.append(i,data[i]);
        }
		const res = await fetch('https://testing.inspiration-feuerwehr.de/radius.php', {
			method: 'POST',
            body: fd
		})
		
		const as = await res.json();
        if(as['worked'] == "1"){
            rpage = 0;
            updateAll();
        }
    }

    async function saveNewEntry(){
        var data = {username: user_name, action: "adddata", table: new_table, attribute: new_attribute, operator: new_operator, value: new_value};
        var fd = new FormData();
        for(var i in data){
            fd.append(i,data[i]);
        }
		const res = await fetch('https://testing.inspiration-feuerwehr.de/radius.php', {
			method: 'POST',
            body: fd
		})
		
		const as = await res.json();
        if(as['worked'] == "1"){
            rpage = 0;
            updateAll();
        }
    }

    function updateAll(){
        updateUserData();
        updateData("check");
        updateData("reply");
        updateUserLogon();
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

    async function updateUserLogon(){
        var data = {username: user_name, action: 'isloggedon'};
        var fd = new FormData();
        for(var i in data){
            fd.append(i,data[i]);
        }
		const res = await fetch('https://testing.inspiration-feuerwehr.de/radius.php', {
			method: 'POST',
            body: fd
		})

        const as = await res.json();
        user_logon = {'logon': as['current'], 'readable': as['current-readable']};
        user_last_logon = {'logon': as['last'], 'readable': as['last-readable']};
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
        border: 2px solid lightgray;
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

    .user-table{
        max-width: 50%;
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

    .head-info div{
        display: inline-block;
    }

    .einwahl{
        float: right;
        margin-right: 80px;
        max-width: 40%;
    }

    .einwahl div{
        border: 2px solid gray;
        border-radius: 10px;

        padding: 0px 20px;
        font-size: 20px;

        margin: 4px;
        float: right;
        /*max-width: 50%;*/
    }

    .addbutton{
        float: right;
        margin-right: 40px;
        margin-top: 10px;
    }

    .del-button{
        width: 30px;
        height: 30px;
        margin: 0px;
        border: 1px solid red;
        background: lightpink;
        color: white;
        text-align: center;
        vertical-align: center;
    }
</style>
