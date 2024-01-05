<script>
    import { Input, Label, Helper, Button } from 'flowbite-svelte';
    import { DarkMode } from 'flowbite-svelte'; 
    import 'js-file-download'
    import axios from 'axios';
    import fileDownload from 'js-file-download';

    let dfd;
    let dfd_copy;
    
    async function getDFD()
    {
        try
        {
            const res = await axios.get('http://localhost:8000/JSON/dfd')
            //await console.log(res.data)
            dfd = await res.data
            dfd_copy = await dfd
        }
        catch(error)
        {
            console.log("BAD ENDPOINT")
        }
    }

    function resetForm()
    {
        dfd = dfd_copy
    }

    function debugDFD()
    {
        console.log(dfd)
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
            //fileDownload
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
            
            <label for="message" class="block mb-2 text-sm font-medium text-gray-900 dark:text-white">{section["ajuda"]}</label>
            <textarea id="message" rows="4" 
            class="block p-2.5 w-full text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500" 
            placeholder="Write your thoughts here..." on:change={debugDFD} bind:value={section["c"][0]["t"]}></textarea>

        {:else if section["type"] == "h"}
            <Label for="first_name" class="mb-2">{section["ajuda"]}</Label>
            <Input type="text" id="first_name" placeholder="Texto" required bind:value={section["c"][0]["t"]}/>
        <!-- {:else if section["type"] == "t"}
            <p>Test table</p> -->
        {/if}
        {/each}
    </form>
    <Button type="download" on:click={send}>Download</Button>
    <Button type="reset" on:click={resetForm}>Reset</Button>
    <!-- <form>
        <div class="grid gap-6 mb-6 md:grid-cols-2">
        <div>
            <Label for="first_name" class="mb-2">First name</Label>
            <Input type="text" id="first_name" placeholder="John" required />
        </div>
        <div>
            <Label for="last_name" class="mb-2">Last name</Label>
            <Input type="text" id="last_name" placeholder="Doe" required />
        </div>
        <div>
            <Label for="company" class="mb-2">Company</Label>
            <Input type="text" id="company" placeholder="Flowbite" required />
        </div>
        <div>
            <Label for="phone" class="mb-2">Phone number</Label>
            <Input type="tel" id="phone" placeholder="123-45-678" pattern={"[0-9]{3}-[0-9]{2}-[0-9]{3}"} required />
        </div>
        <div>
            <Label for="website" class="mb-2">Website URL</Label>
            <Input type="url" id="website" placeholder="flowbite.com" required />
        </div>
        <div>
            <Label for="visitors" class="mb-2">Unique visitors (per month)</Label>
            <Input type="number" id="visitors" placeholder="" required />
        </div>
        </div>
        <div class="mb-6">
        <Label for="email" class="mb-2">Email address</Label>
        <Input type="email" id="email" placeholder="john.doe@company.com" required />
        </div>
        <div class="mb-6">
        <Label for="password" class="mb-2">Password</Label>
        <Input type="password" id="password" placeholder="•••••••••" required />
        </div>
        <div class="mb-6">
        <Label for="confirm_password" class="mb-2">Confirm password</Label>
        <Input type="password" id="confirm_password" placeholder="•••••••••" required />
        </div>
        <Checkbox class="mb-6 space-x-1" required>
        I agree with the <A href="/" class="text-primary-700 dark:text-primary-600 hover:underline">terms and conditions</A>.
        </Checkbox>
        <Button type="submit">Mandar</Button>
        <Button type="download">Download</Button>
  </form> -->
</main>
{:catch error}
<h1 class="text-6xl dark:text-white">{error.message}</h1>
{/await}