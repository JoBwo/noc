<div>
    <h2>Vertrag {contract.name}</h2>

    {#if mode === 0}
        <div class="reload-btn">
            <button on:click={loadParts}>Aktualisieren</button>
        </div>
        <div class="new-entry">
            <span class="title">Neuen Bestandteil hinzufügen:</span>
            <select bind:value={newdata.type}>
                <option selected disabled>Bestandteil wählen</option>
                <option value="radius">Radius-Account</option>
                <option value="cpe">CPE</option>
            </select>
            <button disabled={newdata.type == "" ? "disabled" : ""} on:click={gotoCreate}>Erstellen</button>
        </div>

        <table class="contract-elements">
        <tr>
            <th>Typ</th>
            <th>Bezeichnung</th>
            <th>Link</th>
        </tr>
        {#each elements as element}
        <tr>
            <td>{element.type}</td>
            <td>{element.name}</td>
            <td>
                <span on:click={e => openLink(element.link)}>&#x21aa; Öffnen</span>
                {#if element.type == "Radius"}
                    <span on:click={e => openLink(element.link + "&rpage=2")}>&#x260E; Einwahlen</span>
                    <span style="float: right;" on:click={e => deleteRadius(element.name)}>&#10006; Löschen</span>
                {/if}
                
            </td>
        </tr>
        {/each}
        </table>
    {:else if mode === 1}
        <div class="dialog-new">
            <h2>{newdata.type == "radius" ? "Neuer Radius-Account" : "Neue CPE"}</h2>
            <input bind:value={newdata.name} type="text" placeholder={newdata.type == "radius" ? "Benutzername" : "Bezeichnung"}>
            <button on:click={createNode}>Erstellen</button>
        </div>
    {/if}
</div>

<style>
    table, td, th, tr{
        border: 2px solid lightgray;
        border-collapse: collapse;
        padding: 10px 14px;
    }

    table{
        margin-left: 20px;
        width: calc(100% - 40px);
    }

    .contract-elements, th, td, tr{
        border: 1px solid white;
        border-collapse: collapse;
        padding: 10px 14px;
    }

    .contract-elements tr:nth-child(odd) {
        background-color: var(--bright-green);
    }

    .contract-elements tr:hover{
        background-color: var(--light-green);
    }


    .contract-elements th, td{
        text-align: left;
        vertical-align: middle;
        padding: 10px;
        border-bottom: 2px solid #ddd;
    }

    .contract-elements th {
        background-color: var(--middle-green);
        color: white;
    }

    .reload-btn{
        display: inline-block;
        margin-left: 20px;
    }

    .new-entry {
        margin-right: 20px;
        float: right;
    }

    .new-entry .title{
        margin-right: 10px;
    }
</style>

<script>

    export let selectedContract = "";

    let newdata = {
        contract: selectedContract,
        mode: "addpart",
        type: "",
        name: ""
    };

    let contract = {
        name: "",
        id: ""
    };

    // Mode 0 = Ansicht, Mode 1 = neuer Bestandteil
    let mode = 0;

    document.title = "Vertrag " + selectedContract;

    let elements = [];

    loadParts();

    async function deleteRadius(id){
        newdata.mode = "delete";
        newdata.type = "radius";
        newdata.name = id;
        var data = newdata;
        var fd = new FormData();
        for(var i in data){
            fd.append(i,data[i]);
        }
		const res = await fetch('https://testing.inspiration-feuerwehr.de/contract.php', {
			method: 'POST',
            body: fd
		})
		
        let answer = await res.json();
        if(answer["worked"] == "1"){
            loadParts();
        }
    }

    async function createNode(){
        newdata.mode = "addpart";
        var data = newdata;
        var fd = new FormData();
        for(var i in data){
            fd.append(i,data[i]);
        }
		const res = await fetch('https://testing.inspiration-feuerwehr.de/contract.php', {
			method: 'POST',
            body: fd
		})
		
        let answer = await res.json();
        if(answer["worked"] == "1"){
            mode = 0;
            loadParts();
        }
    }

    function gotoCreate(){
        if(newdata.type == ""){
            return;
        }

        mode = 1;
        newdata.name = "";
    }

    async function loadParts(){
        var data = {contract: parseInt(selectedContract), mode: "fetchparts"};
        var fd = new FormData();
        for(var i in data){
            fd.append(i,data[i]);
        }
		const res = await fetch('https://testing.inspiration-feuerwehr.de/contract.php', {
			method: 'POST',
            body: fd
		})
		
        let temp = await res.json();
        if(temp["worked"] == "0"){
            return;
        }
        contract = temp["data"];
        elements = temp["elements"];
    }

    function openLink(link){
        window.open(link, "_blank").focus();
    }
</script>