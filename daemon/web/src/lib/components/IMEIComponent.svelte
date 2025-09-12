<script lang="ts">
    import { user_action_req } from '$lib/utils.svelte';

    let imei: string = $state('');
    let loading: boolean = $state(true);
    let error: string = $state('');

    // Function to fetch the IMEI
    async function fetchIMEI() {
        loading = true;
        error = '';
        try {
            const response = await user_action_req('GET', '/api/imei', 'Failed to fetch IMEI');
            if (response) {
                imei = response.trim();
            }
        } catch (err) {
            error = 'Failed to fetch IMEI';
            console.error(err);
        } finally {
            loading = false;
        }
    }

    // Function to change the IMEI
    async function changeIMEI() {
        if (confirm('Are you sure you want to change the IMEI?')) {
            loading = true;
            error = '';
            try {
                await user_action_req('POST', '/api/change-imei', 'Failed to change IMEI');
                // Refresh the IMEI after changing it
                await fetchIMEI();
            } catch (err) {
                error = 'Failed to change IMEI';
                console.error(err);
            } finally {
                loading = false;
            }
        }
    }

    // Function to reboot the system
    async function rebootSystem() {
        if (confirm('Are you sure you want to reboot the system?')) {
            loading = true;
            error = '';
            try {
                await user_action_req('POST', '/api/reboot', 'Failed to reboot system');
            } catch (err) {
                error = 'Failed to reboot system';
                console.error(err);
            } finally {
                loading = false;
            }
        }
    }

    // Fetch IMEI when component mounts
    $effect(() => {
        fetchIMEI();
    });
</script>

<div class="bg-amber-900 rounded-lg p-4 shadow">
    <div class="flex justify-between items-center mb-2">
        <h3 class="text-lg font-semibold text-white">Device IMEI</h3>
        <div class="flex space-x-2">
            <button
                class="bg-blue-600 hover:bg-blue-700 text-white px-3 py-1 rounded text-sm disabled:opacity-50"
                onclick={changeIMEI}
                disabled={loading}
            >
                Change IMEI
            </button>
            <button
                class="bg-amber-700 hover:bg-amber-600 text-white px-3 py-1 rounded text-sm disabled:opacity-50"
                onclick={rebootSystem}
                disabled={loading}
            >
                Reboot
            </button>
        </div>
    </div>
    
    <div class="mt-2">
        {#if loading}
            <p class="text-white">Loading...</p>
        {:else if error}
            <p class="text-red-300">{error}</p>
        {:else}
            <p class="text-white">
                <span class="font-medium">IMEI:</span> {imei || 'Not available'}
            </p>
        {/if}
    </div>
</div>