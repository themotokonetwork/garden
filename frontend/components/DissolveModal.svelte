<script lang="ts">
  import Button from '@themotokonetwork/fpdao-ui/components/Button.svelte';
  import Modal from '@themotokonetwork/fpdao-ui/components/Modal.svelte';
  import Loader from '@themotokonetwork/fpdao-ui/components/Loader.svelte';
  import { getContext } from 'svelte';

  import { store } from '../store';
  import { Neuron } from '../../declarations/main/main.did';

  export let neuron: Neuron;

  let refreshGarden = getContext('refreshGarden') as (target: string) => Promise<void>;

  let modalOpen = false;
  let loading = false;
  let success = false;
  let error = '';

  export function toggleModal() {
    modalOpen = !modalOpen;
    loading = false;
    error = '';
  }

  async function startDissolve() {
    loading = true;

    let stakeRes = await $store.gardenActor.dissolveNeuron(neuron.id);
    if ('err' in stakeRes) {
      error = stakeRes.err;
      return;
    }

    await refreshGarden('staked');

    loading = false;
    modalOpen = false;
  }
</script>

{#if modalOpen}
  <Modal title="Extract" {toggleModal}>
    <div class="flex gap-3 flex-col flex-1 justify-center items-center">
      {#if error}
        <div class="text-red-700 text-xl flex flex-col grow">
          <div>Error</div>
          <div>{error}</div>
        </div>
      {:else if success}
        <div class="text-xl text-green-700">
          Success!
        </div>
      {:else}
        <div class="text-xl flex flex-col gap-4">
          <div>Extract delay is 30 days.</div>
          <div>During this period, the flower does not produce seeds.</div>
          <div>After 30 days you can withdraw the flower.</div>
        </div>
        <Button style="w-auto px-20 py-8 h-10 mt-10 rounded-[55px]" disabled={loading} on:click={startDissolve}>
          {#if loading}
            <Loader {loading}></Loader>
          {:else}
            Extract
          {/if}
        </Button>
      {/if}
    </div>
  </Modal>
{/if}