<script lang="ts">
  import { getContext } from 'svelte';
  import Button from '@themotokonetwork/fpdao-ui/components/Button.svelte';
  import Modal from '@themotokonetwork/fpdao-ui/components/Modal.svelte';
  import Loader from '@themotokonetwork/fpdao-ui/components/Loader.svelte';
  import { store } from '../store';
  import { Neuron } from '../../declarations/main/main.did';
  import {getCollectionDailyRewards, getNeuronCollection} from '../utils';

  export let neuron: Neuron;

  let refreshGarden = getContext('refreshGarden') as (target: string) => Promise<void>;

  let dailyReward = getCollectionDailyRewards(getNeuronCollection(neuron));
  let modalOpen = false;
  let loading = false;
  let success = false;
  let error = '';

  export function toggleModal() {
    modalOpen = !modalOpen;
    loading = false;
    error = '';
  }

  async function restake() {
    loading = true;

    let stakeRes = await $store.gardenActor.restake(neuron.id);
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
  <Modal title="Replant" {toggleModal}>
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
          <div>You are about to replant your flower.</div>
          <div>Planted flower will give you {dailyReward} SEED tokens every day.</div>
          <div>If you decide to extract the flower you have to wait 30 day before you can withdraw the flower.</div>
        </div>
        <Button style="w-auto px-20 py-8 h-10 mt-10 rounded-[55px]" disabled={loading} on:click={restake}>
          {#if loading}
            <Loader {loading}></Loader>
          {:else}
            Replant
          {/if}
        </Button>
      {/if}
    </div>
  </Modal>
{/if}