<script lang="ts">
  import Button from '@themotokonetwork/fpdao-ui/components/Button.svelte';

  import { UserRes } from '../../declarations/main/main.did';
  import WithdrawModal from './WithdrawModal.svelte';
  import {applyHalvings} from '../utils';

  export let user: UserRes;

  let withdrawModal: WithdrawModal;

  let trilogyCount = 0;
  let growthRate = 0;
  let trilogyBonus = 0;

  let toggleWithdrawModal = () => {
    withdrawModal.toggleModal();
  };

  let getFlowerCount = (collection: string) => {
    return user.neurons.filter(n => 'DissolveDelay' in n.dissolveState && collection in n.flower.collection).length;
  };

  $: {
    trilogyCount = Math.min(...[1, getFlowerCount('BTCFlower'), getFlowerCount('ETHFlower'), getFlowerCount('ICPFlower')]);
    growthRate = (applyHalvings(getFlowerCount('BTCFlower') * 2) + applyHalvings(getFlowerCount('ETHFlower') * 0.5) + applyHalvings(getFlowerCount('ICPFlower') * 0.5) + applyHalvings(getFlowerCount('BTCFlowerGen2') * 0.5))
    trilogyBonus = growthRate * trilogyCount * 15 / 100;
    growthRate += trilogyBonus;
  }
</script>

{#if user}
  <div class="flex flex-col gap-4 dark:border-gray-100 dark:text-white">
    <div class="text-xl">Balance: <span class="font-bold">{(Number(user.rewards) / 1e8).toFixed(4)}</span> SEED</div>

    <Button on:click={toggleWithdrawModal} style="px-8 mb-4">Withdraw</Button>

    <div>
        <div>Growth rate: <span class="font-bold">{growthRate.toFixed(4)}</span> SEED/day</div>
        <div class="ml-5 mt-1">
        <div><span class="font-semibold">{getFlowerCount('BTCFlower')}</span> BTC Flower <span class="font-semibold">x {applyHalvings(2)}</span> SEED = <span class="font-semibold">{applyHalvings(getFlowerCount('BTCFlower') * 2)}</span> SEED/day</div>
        <div><span class="font-semibold">{getFlowerCount('ETHFlower')}</span> ETH Flower <span class="font-semibold">x {applyHalvings(0.5)}</span> SEED = <span class="font-semibold">{applyHalvings(getFlowerCount('ETHFlower') * 0.5)}</span> SEED/day</div>
        <div><span class="font-semibold">{getFlowerCount('ICPFlower')}</span> ICP Flower <span class="font-semibold">x {applyHalvings(0.5)}</span> SEED = <span class="font-semibold">{applyHalvings(getFlowerCount('ICPFlower') * 0.5)}</span> SEED/day</div>
        <div><span class="font-semibold">{getFlowerCount('BTCFlowerGen2')}</span> BTC Flower Gen 2.0 <span class="font-semibold">x {applyHalvings(0.5)}</span> SEED = <span class="font-semibold">{applyHalvings(getFlowerCount('BTCFlowerGen2') * 0.5)}</span> SEED/day</div>
        <div><span class="font-semibold">+{trilogyCount * 15}%</span> trilogy bonus = <span class="font-semibold">{trilogyBonus}</span> SEED/day</div>
      </div>
    </div>
  </div>

  <WithdrawModal {user} bind:this={withdrawModal}></WithdrawModal>
{/if}