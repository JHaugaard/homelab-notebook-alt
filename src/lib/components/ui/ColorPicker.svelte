<script lang="ts">
	interface Props {
		value?: string;
		id?: string;
		class?: string;
	}

	let { value = $bindable(''), id, class: className = '' }: Props = $props();

	// Curated preset colors suitable for tags
	const presetColors = [
		// Row 1: Reds to Oranges
		'#EF4444', '#F97316', '#F59E0B', '#EAB308',
		// Row 2: Greens
		'#84CC16', '#22C55E', '#10B981', '#14B8A6',
		// Row 3: Blues
		'#06B6D4', '#0EA5E9', '#3B82F6', '#6366F1',
		// Row 4: Purples to Pinks
		'#8B5CF6', '#A855F7', '#D946EF', '#EC4899',
		// Row 5: Neutrals
		'#6B7280', '#78716C', '#71717A', '#64748B'
	];

	let showCustomInput = $state(false);
	let customValue = $state('');

	// Check if current value is a preset or custom
	let isCustomColor = $derived(value && !presetColors.includes(value.toUpperCase()) && !presetColors.includes(value));

	function selectColor(color: string) {
		value = color;
		showCustomInput = false;
	}

	function clearColor() {
		value = '';
		showCustomInput = false;
		customValue = '';
	}

	function toggleCustomInput() {
		showCustomInput = !showCustomInput;
		if (showCustomInput && value) {
			customValue = value;
		}
	}

	function handleCustomInput(e: Event) {
		const input = e.target as HTMLInputElement;
		customValue = input.value;
		// Auto-apply if it looks like a valid hex color
		if (/^#[0-9A-Fa-f]{6}$/.test(customValue)) {
			value = customValue.toUpperCase();
		}
	}
</script>

<div class="space-y-3 {className}" {id}>
	<!-- Color grid -->
	<div class="grid grid-cols-8 gap-1.5">
		{#each presetColors as color}
			<button
				type="button"
				class="w-7 h-7 rounded-md border-2 transition-all hover:scale-110 focus:outline-none focus:ring-2 focus:ring-offset-1 focus:ring-blue-500"
				class:border-[var(--color-border)]={value?.toUpperCase() !== color}
				class:border-[var(--color-text)]={value?.toUpperCase() === color}
				class:ring-2={value?.toUpperCase() === color}
				class:ring-offset-1={value?.toUpperCase() === color}
				style="background-color: {color}"
				onclick={() => selectColor(color)}
				aria-label="Select color {color}"
			></button>
		{/each}
	</div>

	<!-- Actions row -->
	<div class="flex items-center gap-2 text-sm">
		<!-- Current color preview or none indicator -->
		{#if value}
			<div class="flex items-center gap-2">
				<div
					class="w-6 h-6 rounded border border-[var(--color-border)]"
					style="background-color: {value}"
				></div>
				<span class="text-[var(--color-text-muted)] font-mono text-xs">{value}</span>
			</div>
		{:else}
			<span class="text-[var(--color-text-muted)] text-xs">No color selected</span>
		{/if}

		<div class="flex-1"></div>

		<!-- Custom color toggle -->
		<button
			type="button"
			class="text-xs text-[var(--color-text-muted)] hover:text-[var(--color-text)] transition-colors"
			onclick={toggleCustomInput}
		>
			{showCustomInput ? 'Hide' : 'Custom'}
		</button>

		<!-- Clear button -->
		{#if value}
			<button
				type="button"
				class="text-xs text-[var(--color-text-muted)] hover:text-red-500 transition-colors"
				onclick={clearColor}
			>
				Clear
			</button>
		{/if}
	</div>

	<!-- Custom hex input -->
	{#if showCustomInput}
		<div class="flex gap-2">
			<input
				type="text"
				value={customValue}
				oninput={handleCustomInput}
				placeholder="#3B82F6"
				class="flex-1 px-2 py-1.5 text-sm font-mono bg-[var(--color-surface)] border border-[var(--color-border)] rounded-md text-[var(--color-text)] placeholder:text-[var(--color-text-muted)] focus:outline-none focus:border-[var(--color-border-strong)]"
			/>
			{#if customValue && /^#[0-9A-Fa-f]{6}$/.test(customValue)}
				<div
					class="w-8 h-8 rounded-md border border-[var(--color-border)]"
					style="background-color: {customValue}"
				></div>
			{/if}
		</div>
		<p class="text-xs text-[var(--color-text-muted)]">
			Enter a hex color code (e.g., #3B82F6)
		</p>
	{/if}
</div>
