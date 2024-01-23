<script>
import { Badge } from 'flowbite-svelte';
import { Button } from 'flowbite-svelte';

let modelList = []
let listOfComponents = 
[
    {type: "nome",  t:"test"},
    {type: "h", c:[{t:"meu titulo"}], htype:0, ajuda:"minha tooltip"},
    {type: "p", c:[{t: "test"}], ajuda:"minha tooltip"},
    {type: "t", rows: 0, cell: 0, cells: [], ajuda:"minha tooltip"}
]

function addComponentToModelList(component)
{
    modelList.push(component)
    console.log(modelList)
}

function removeComponentFromModelList()
{

}

function sendModel()
{

}
</script>

<main>
<div class="flex flex-row justify-center ">

    <div class="flex flex-col">
        {#each modelList as component}
            {#if component.type == "nome"}
            <Badge large>Nome do Documento</Badge>
            {:else if component.type == "h"}
                <Badge large>Titulo</Badge>
            {:else if component.type == "p"}
                <Badge large>Paragrafo</Badge>
            {:else}
                <Badge large>Tabela</Badge>
            {/if}
        <Button color="red">Remover</Button>
        {/each}
    </div>
    
    
    <div class="grid col-2 gap-2 grid-rows-2">
        {#each listOfComponents as component}
        <div>
            {#if component.type == "nome"}
                <Badge large>Nome do Documento</Badge>
            {:else if component.type == "h"}
                <Badge large>Titulo</Badge>
            {:else if component.type == "p"}
                <Badge large>Paragrafo</Badge>
            {:else}
                <Badge large>Tabela</Badge>
            {/if}
            <Button on:click={() => 
            {
                modelList = [...modelList, component]
                console.log(modelList)
            }} color="green">Adicionar</Button>
        </div>
        {/each}
    </div>
    
</div>
<button>Criar</button>
</main>