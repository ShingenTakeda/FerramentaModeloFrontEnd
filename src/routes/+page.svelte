<script>
    import { Input, Label, Helper, Button } from 'flowbite-svelte';
    import { DarkMode } from 'flowbite-svelte'; 
    import 'js-file-download'
    import axios from 'axios';
    import fileDownload from 'js-file-download';
    import MTable from '../components/MTable.svelte';

    let dfd;
    let dfd_copy;
    
    async function getDFD()
    {
        try
        {
            const res = await axios.get('http://localhost:8000/JSON/dfd')
            const ares = await axios.get('http://localhost:8000/JSON/dfd')
            dfd = await res.data
            dfd_copy = await ares.data
        }
        catch(error)
        {
            console.log("BAD ENDPOINT")
        }
    }

    function debugDFD()
    {
        //console.log(dfd)
    }

    async function send()
    {
        axios(
        {
            method:"POST",
            url:"http://127.0.0.1:8000/docx", 
            data: dfd,
            responseType: 'blob'
        })
        .then(function (response)
        {
            fileDownload(response.data, 'dfd.docx');
        })
        .catch(error => console.log("ERROR: " + error))
    }
</script>

<DarkMode />
{#await getDFD()}
<h1 class="text-6xl dark:text-white">Fetching Stuff...</h1>
{:then}
<main>
    <h1 class="text-6xl dark:text-white">Nome do Modelo: <br> {dfd[0]["t"]}</h1>
    <form>
        {#each dfd as section}
        {#if section["type"] == "p"}
            
            <label for="message" class="block mb-2 text-2xl font-medium text-gray-900 dark:text-white">{section["ajuda"]}</label>
            <textarea id="message" rows="4" 
            class="block p-2.5 w-full text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500" 
            placeholder="Write your thoughts here..." on:change={debugDFD} bind:value={section["c"][0]["t"]}></textarea>

        {:else if section["type"] == "h"}
            <Label for="first_name" class="2xl ">{section["ajuda"]}</Label>
            <Input type="text" id="first_name" placeholder="Texto" required bind:value={section["c"][0]["t"]}/>
        <!-- {:else if section["type"] == "t"}
            <p>Test table</p> -->
        {:else if section["type"] == "t"}
            <br>
            <h4 class="text-2xl dark:text-white">Tabela</h4>
            <!-- <DTable bind:tableRows={section["cells"]}></DTable> -->
            <MTable bind:tableRows={section["cells"]}></MTable>
        {/if}
        {/each}
        
    </form>
    <Button type="download" on:click={send}>Download</Button>
    <Button type="log" on:click={debugDFD}>Log</Button>
    <Button type="reset" on:click={()=>
    {
        dfd = dfd_copy
    }
    }>Reset</Button>
</main>
{:catch error}
<h1 class="text-6xl dark:text-white">{error.message}</h1>
{/await}