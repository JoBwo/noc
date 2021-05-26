<div style="overflow: auto; height: 100%;">

    {#if selectedContract != ""}
        <ContractView selectedContract={selectedContract} baseurl={baseurl}/>
    {:else}
        <h2>Verträge</h2>
        <button on:click={() => loadContract("1")}>Testvertrag öffnen</button>
    {/if}

</div>

<script>

    import ContractView from './ContractView.svelte';

    let selectedContract = "";

    export let baseurl = "";

    document.title = "Verträge";

    const queryString = window.location.search;
	const urlParams = new URLSearchParams(queryString);
    

    if(isset(urlParams.get('contract'))){
		selectedContract = urlParams.get('contract');
	}

    function loadContract(id){
        selectedContract = id;
        updateContract();
    }

    function updateContract() {
		const search = window.location.search;
		const urlParams = new URLSearchParams(search);
		if(selectedContract === ""){
            urlParams.delete('contract');
        }else {
            urlParams.set('contract', selectedContract);
        }
		window.location.search = urlParams.toString();
    }

    function isset(v){
		return (v !== 'undefined' && v !== null);
	}

</script>