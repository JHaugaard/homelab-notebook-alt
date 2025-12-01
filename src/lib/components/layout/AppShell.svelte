<script lang="ts">
	import type { Snippet } from 'svelte';
	import { Menu } from 'lucide-svelte';
	import Sidebar from './Sidebar.svelte';
	import { sidebarOpen } from '$lib/stores';

	interface Props {
		children: Snippet;
	}

	let { children }: Props = $props();
</script>

<div class="flex h-screen bg-[var(--color-background)]">
	<!-- Mobile sidebar backdrop -->
	{#if $sidebarOpen}
		<button
			class="fixed inset-0 z-30 sidebar-backdrop md:hidden animate-fade-in"
			onclick={() => ($sidebarOpen = false)}
			aria-label="Close sidebar"
		></button>
	{/if}

	<!-- Sidebar -->
	<Sidebar />

	<!-- Main content area -->
	<main class="flex-1 flex flex-col overflow-hidden w-full md:w-auto">
		<!-- Mobile header with menu button -->
		<div class="md:hidden flex items-center gap-3 px-4 py-3 border-b border-[var(--color-border)] bg-[var(--color-surface)]">
			<button
				class="p-2 -ml-2 rounded-lg hover:bg-[var(--color-surface-hover)] text-[var(--color-text-secondary)]"
				onclick={() => ($sidebarOpen = true)}
				aria-label="Open menu"
			>
				<Menu class="w-5 h-5" />
			</button>
			<span class="font-display text-lg text-[var(--color-text)]">Homelab Notebook</span>
		</div>

		{@render children()}
	</main>
</div>
