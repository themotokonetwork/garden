<script lang="ts">
  import { onMount } from "svelte";
  import "@themotokonetwork/fpdao-ui/styles/global.css";
  import Header from "@themotokonetwork/fpdao-ui/components/Header.svelte";
  import Footer from "@themotokonetwork/fpdao-ui/components/Footer.svelte";
  import Garden from './Garden.svelte';
  import { authStore } from '../store';

  onMount(async () => {
    await authStore.checkConnections();
  });
</script>

<Header {authStore} />
<Garden></Garden>
<Footer {authStore} />
