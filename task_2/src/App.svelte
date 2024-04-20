<script lang="ts">
  import { onMount } from 'svelte';
  let codes:string[]= [];

  let valuefirst = 0;
  let valuesec =0;
  let data = [];
  let firstvalut = "AED";
  let secondvalut = "AED";


onMount(async () => {
    const response = await fetch('https://v6.exchangerate-api.com/v6/82fa454663d2f653216c99c6/codes');
    const datacodes = await response.json();
    codes = datacodes.supported_codes.map((item: any[]) => item[0]);;
  });

const fetchDataForFirst = async () => {
  try {
    const response = await fetch(`https://v6.exchangerate-api.com/v6/82fa454663d2f653216c99c6/pair/${firstvalut}/${secondvalut}/${valuefirst}`);
    
      data = await response.json();
      await new Promise(resolve => setTimeout(resolve,1000)); 
      valuesec = await data.conversion_result;
      console.log(data)
 
  } catch (error) {
    console.error('Ошибка:', error);
  }
  
};



const fetchDataForSecond = async () =>{
  try{
    const response = await fetch(`https://v6.exchangerate-api.com/v6/82fa454663d2f653216c99c6/pair/${secondvalut}/${firstvalut}/${valuesec}`);
      data = await response.json();
      await new Promise(resolve => setTimeout(resolve,1000)); 
      valuefirst =await data.conversion_result;
  }catch(error){
    console.error('Ошибка:', error);
  }
}


function handleChangeFirst(event:Event) {
    const target = event.target as HTMLSelectElement;
    firstvalut = target.value;
    console.log(firstvalut);
    fetchDataForFirst();
  }

  function handleChangeSecond(event:Event) {
    const target = event.target as HTMLSelectElement;
    secondvalut = target.value;
    console.log(secondvalut);
    fetchDataForSecond();
  }

</script>

<main>
<div class="converter">
  <div class="converter-firstvalue">
    <a>Первая валюта и значение</a>
    <div>
      <select  on:change={handleChangeFirst}>
        {#each codes as code}
        <option value="{code}">{code}</option>
    {/each}
      </select>
      <input bind:value={valuefirst} on:input={fetchDataForFirst} />
    </div>
  </div>

  <div class="converter-secvalue">
    <a>Вторая валюта и значение</a>
    <div>
      <select on:change={handleChangeSecond}>
        {#each codes as code}
        <option value="{code}">{code}</option>
    {/each}
      </select>
      <input bind:value={valuesec} on:input={fetchDataForSecond} />
    </div>
   
  </div>
</div>

</main>

<style>
   select{
    width: 100px;
    color: white;
  }

  .converter-secvalue{
    margin-top: 20px;
  }

 
</style>
