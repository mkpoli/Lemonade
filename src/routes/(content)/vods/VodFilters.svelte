<script lang="ts">
	import {
		CHARACTER_NAMES,
		MAP_NAMES,
		PUS_CHARACTERS,
		SCISORS_CHARACTERS,
		URBINO_CHARACTERS,
		type Character,
		type GameMap,
		type Rank
	} from '$lib/data/game';
	import {
		VOD_PLATFORMS_LABELS,
		VOD_TYPES,
		VOD_TYPES_LABELS,
		type VodPlatform
	} from '$lib/data/vod';
	import { RANKS } from '$lib/data/game';
	import CharacterIcon from '$lib/components/CharacterIcon.svelte';
	import * as m from '$lib/paraglide/messages';
	import RankIcon from '$lib/components/RankIcon.svelte';

	let {
		platforms = $bindable([]),
		maps = $bindable([]),
		servers = $bindable([]),
		characters = $bindable([]),
		seasons = $bindable([]),
		players = $bindable([]),
		ranks = $bindable([]),
		onChange
	} = $props<{
		platforms: VodPlatform[];
		maps: GameMap[];
		servers: (string | null)[];
		characters: (string | null)[];
		seasons: (string | null)[];
		players: (string | null)[];
		ranks: (string | null)[];
		onChange?: (e: { detail: any }) => void;
	}>();

	function isString(val: unknown): val is string {
		return typeof val === 'string';
	}

	let selectedPlatforms: string[] = $state([]);
	let selectedMaps: string[] = $state([]);
	let selectedServers: string[] = $state([]);
	let selectedCharacters: string[] = $state([]);
	let selectedSeasons: string[] = $state([]);
	let selectedPlayers: string[] = $state([]);
	let selectedRanks: string[] = $state([]);
	let selectedTypes: string[] = $state([]);
	function toggleFilter(arr: string[], value: string) {
		if (arr.includes(value)) {
			return arr.filter((v) => v !== value);
		} else {
			return [...arr, value];
		}
	}

	// Group similar ranks for filtering (e.g., Quark I, II, III -> Quark)
	function getRankGroup(rank: string): string {
		if (rank.startsWith('Substance')) return 'Substance';
		if (rank.startsWith('Molecule')) return 'Molecule';
		if (rank.startsWith('Atom')) return 'Atom';
		if (rank.startsWith('Proton')) return 'Proton';
		if (rank.startsWith('Neutron')) return 'Neutron';
		if (rank.startsWith('Electron')) return 'Electron';
		if (rank.startsWith('Quark')) return 'Quark';
		if (rank.startsWith('Superstring')) return 'Superstring';
		if (rank.startsWith('Singularity')) return 'Singularity';
		return rank;
	}

	const RANK_GROUP_NAMES: Record<string, () => string> = {
		Substance: m.Substance,
		Molecule: m.Molecule,
		Atom: m.Atom,
		Proton: m.Proton,
		Neutron: m.Neutron,
		Electron: m.Electron,
		Quark: m.Quark,
		Superstring: m.Superstring,
		Singularity: m.Singularity
	};

	// Get unique rank groups from the provided ranks, sorted by first occurrence in RANKS
	const rankGroupsSet = new Set(ranks.filter(isString).map(getRankGroup));
	const rankGroups = Array.from(rankGroupsSet).sort((a, b) => {
		const aIdx = RANKS.findIndex((r) => getRankGroup(r) === a);
		const bIdx = RANKS.findIndex((r) => getRankGroup(r) === b);
		return aIdx - bIdx;
	});

	$effect(() => {
		onChange?.({
			detail: {
				selectedPlatforms,
				selectedMaps,
				selectedServers,
				selectedCharacters,
				selectedSeasons,
				selectedPlayers,
				selectedRanks,
				selectedTypes
			}
		});
	});
</script>

<div
	class="mb-6 flex flex-wrap gap-6 overflow-x-auto rounded-xl bg-black/70 p-4 shadow-lg ring-1 ring-amber-300/10"
>
	<div class="min-w-[160px]">
		<label class="mb-2 block text-sm font-semibold text-amber-200">{m.platform()}</label>
		<div class="flex flex-wrap gap-2">
			<button
				type="button"
				class={`rounded border px-3 py-1 text-sm font-medium transition-colors focus:ring-2 focus:ring-amber-400 focus:outline-none ${selectedPlatforms.length === 0 ? 'border-amber-500 bg-gradient-to-r from-yellow-300 to-amber-500 text-black shadow' : 'border-gray-700 bg-zinc-900 text-amber-200 hover:border-amber-400 hover:bg-amber-400/10'}`}
				onclick={() => (selectedPlatforms = [])}>{m.all()}</button
			>
			{#each platforms.filter(isString) as platform}
				<button
					type="button"
					class={`rounded border px-3 py-1 text-sm font-medium transition-colors focus:ring-2 focus:ring-amber-400 focus:outline-none ${selectedPlatforms.includes(platform) ? 'border-amber-500 bg-gradient-to-r from-yellow-300 to-amber-500 text-black shadow' : 'border-gray-700 bg-zinc-900 text-amber-200 hover:border-amber-400 hover:bg-amber-400/10'}`}
					onclick={() => (selectedPlatforms = toggleFilter(selectedPlatforms, platform))}
					>{VOD_PLATFORMS_LABELS[platform as VodPlatform]}</button
				>
			{/each}
		</div>
	</div>
	<div class="min-w-[160px]">
		<label class="mb-2 block text-sm font-semibold text-amber-200">{m.map()}</label>
		<div class="flex flex-wrap gap-2">
			<button
				type="button"
				class={`rounded border px-3 py-1 text-sm font-medium transition-colors focus:ring-2 focus:ring-amber-400 focus:outline-none ${selectedMaps.length === 0 ? 'border-amber-500 bg-gradient-to-r from-yellow-300 to-amber-500 text-black shadow' : 'border-gray-700 bg-zinc-900 text-amber-200 hover:border-amber-400 hover:bg-amber-400/10'}`}
				onclick={() => (selectedMaps = [])}>{m.all()}</button
			>
			{#each maps.filter(isString) as map}
				<button
					type="button"
					class={`rounded border px-3 py-1 text-sm font-medium transition-colors focus:ring-2 focus:ring-amber-400 focus:outline-none ${selectedMaps.includes(map) ? 'border-amber-500 bg-gradient-to-r from-yellow-300 to-amber-500 text-black shadow' : 'border-gray-700 bg-zinc-900 text-amber-200 hover:border-amber-400 hover:bg-amber-400/10'}`}
					onclick={() => (selectedMaps = toggleFilter(selectedMaps, map))}
					>{MAP_NAMES[map as GameMap]()}</button
				>
			{/each}
		</div>
	</div>
	<div class="min-w-[160px]">
		<label class="mb-2 block text-sm font-semibold text-amber-200">{m.server()}</label>
		<div class="flex flex-wrap gap-2">
			<button
				type="button"
				class={`rounded border px-3 py-1 text-sm font-medium transition-colors focus:ring-2 focus:ring-amber-400 focus:outline-none ${selectedServers.length === 0 ? 'border-amber-500 bg-gradient-to-r from-yellow-300 to-amber-500 text-black shadow' : 'border-gray-700 bg-zinc-900 text-amber-200 hover:border-amber-400 hover:bg-amber-400/10'}`}
				onclick={() => (selectedServers = [])}>{m.all()}</button
			>
			{#each servers.filter(isString) as server}
				<button
					type="button"
					class={`rounded border px-3 py-1 text-sm font-medium transition-colors focus:ring-2 focus:ring-amber-400 focus:outline-none ${selectedServers.includes(server) ? 'border-amber-500 bg-gradient-to-r from-yellow-300 to-amber-500 text-black shadow' : 'border-gray-700 bg-zinc-900 text-amber-200 hover:border-amber-400 hover:bg-amber-400/10'}`}
					onclick={() => (selectedServers = toggleFilter(selectedServers, server))}>{server}</button
				>
			{/each}
		</div>
	</div>
	<div class="min-w-[160px]">
		<label class="mb-2 block text-sm font-semibold text-amber-200">{m.character()}</label>
		<div class="flex flex-wrap gap-2">
			<button
				type="button"
				class={`rounded border px-3 py-1 text-sm font-medium transition-colors focus:ring-2 focus:ring-amber-400 focus:outline-none ${selectedCharacters.length === 0 ? 'border-amber-500 bg-gradient-to-r from-yellow-300 to-amber-500 text-black shadow' : 'border-gray-700 bg-zinc-900 text-amber-200 hover:border-amber-400 hover:bg-amber-400/10'}`}
				onclick={() => (selectedCharacters = [])}>{m.all()}</button
			>
			{#snippet characterOption(character: Character, faction: 'PUS' | 'Scissors' | 'Urbino')}
				<button
					type="button"
					class={[
						'rounded border py-1 pr-3 pl-2.5 text-sm font-medium transition-colors focus:ring-2 focus:ring-amber-400 focus:outline-none',
						'flex items-center gap-1',
						selectedCharacters.includes(character as string)
							? 'border-amber-500 bg-gradient-to-r from-yellow-300 to-amber-500 text-black shadow'
							: [
									'border-gray-700 text-amber-200  hover:border-amber-400 ',
									faction === 'PUS' && 'bg-gradient-to-r from-blue-300/20 to-cyan-500/20',
									faction === 'Urbino' && 'bg-gradient-to-r from-yellow-300/20 to-amber-500/20',
									faction === 'Scissors' && 'bg-gradient-to-r from-red-300/20 to-rose-500/20',
									false && ' bg-zinc-900 hover:bg-amber-400/10'
								]
					]}
					onclick={() =>
						(selectedCharacters = toggleFilter(selectedCharacters, character as string))}
				>
					<CharacterIcon class="h-5 w-5" {character} />
					{CHARACTER_NAMES[character as Character]()}</button
				>
			{/snippet}

			{#each PUS_CHARACTERS.filter((c) => characters.includes(c)) as character}
				{@render characterOption(character, 'PUS')}
			{/each}

			{#each SCISORS_CHARACTERS.filter((c) => characters.includes(c)) as character}
				{@render characterOption(character, 'Scissors')}
			{/each}

			{#each URBINO_CHARACTERS.filter((c) => characters.includes(c)) as character}
				{@render characterOption(character, 'Urbino')}
			{/each}
		</div>
	</div>
	<div class="min-w-[160px]">
		<label class="mb-2 block text-sm font-semibold text-amber-200">{m.season()}</label>
		<div class="flex flex-wrap gap-2">
			<button
				type="button"
				class={`rounded border px-3 py-1 text-sm font-medium transition-colors focus:ring-2 focus:ring-amber-400 focus:outline-none ${selectedSeasons.length === 0 ? 'border-amber-500 bg-gradient-to-r from-yellow-300 to-amber-500 text-black shadow' : 'border-gray-700 bg-zinc-900 text-amber-200 hover:border-amber-400 hover:bg-amber-400/10'}`}
				onclick={() => (selectedSeasons = [])}>{m.all()}</button
			>
			{#each seasons.filter(isString) as season}
				<button
					type="button"
					class={`rounded border px-3 py-1 text-sm font-medium transition-colors focus:ring-2 focus:ring-amber-400 focus:outline-none ${selectedSeasons.includes(season) ? 'border-amber-500 bg-gradient-to-r from-yellow-300 to-amber-500 text-black shadow' : 'border-gray-700 bg-zinc-900 text-amber-200 hover:border-amber-400 hover:bg-amber-400/10'}`}
					onclick={() => (selectedSeasons = toggleFilter(selectedSeasons, season))}>{season}</button
				>
			{/each}
		</div>
	</div>
	<div class="min-w-[160px]">
		<label class="mb-2 block text-sm font-semibold text-amber-200">{m.player()}</label>
		<div class="flex flex-wrap gap-2">
			<button
				type="button"
				class={`rounded border px-3 py-1 text-sm font-medium transition-colors focus:ring-2 focus:ring-amber-400 focus:outline-none ${selectedPlayers.length === 0 ? 'border-amber-500 bg-gradient-to-r from-yellow-300 to-amber-500 text-black shadow' : 'border-gray-700 bg-zinc-900 text-amber-200 hover:border-amber-400 hover:bg-amber-400/10'}`}
				onclick={() => (selectedPlayers = [])}>{m.all()}</button
			>
			{#each players.filter(isString) as player}
				<button
					type="button"
					class={`rounded border px-3 py-1 text-sm font-medium transition-colors focus:ring-2 focus:ring-amber-400 focus:outline-none ${selectedPlayers.includes(player) ? 'border-amber-500 bg-gradient-to-r from-yellow-300 to-amber-500 text-black shadow' : 'border-gray-700 bg-zinc-900 text-amber-200 hover:border-amber-400 hover:bg-amber-400/10'}`}
					onclick={() => (selectedPlayers = toggleFilter(selectedPlayers, player))}>{player}</button
				>
			{/each}
		</div>
	</div>
	<div class="min-w-[160px]">
		<label class="mb-2 block text-sm font-semibold text-amber-200">{m.rank()}</label>
		<div class="flex flex-wrap gap-2">
			<button
				type="button"
				class={`rounded border px-3 py-1 text-sm font-medium transition-colors focus:ring-2 focus:ring-amber-400 focus:outline-none ${selectedRanks.length === 0 ? 'border-amber-500 bg-gradient-to-r from-yellow-300 to-amber-500 text-black shadow' : 'border-gray-700 bg-zinc-900 text-amber-200 hover:border-amber-400 hover:bg-amber-400/10'}`}
				onclick={() => (selectedRanks = [])}>{m.all()}</button
			>
			{#each rankGroups as group}
				<button
					type="button"
					class={[
						'rounded border px-3 py-1 text-sm font-medium transition-colors focus:ring-2 focus:ring-amber-400 focus:outline-none',
						'flex items-center gap-1',
						selectedRanks.includes(group as string)
							? 'border-amber-500 bg-gradient-to-r from-yellow-300 to-amber-500 text-black shadow'
							: 'border-gray-700 bg-zinc-900 text-amber-200 hover:border-amber-400 hover:bg-amber-400/10'
					]}
					onclick={() => (selectedRanks = toggleFilter(selectedRanks, group as string))}
				>
					<RankIcon rank={group as Rank} />
					{RANK_GROUP_NAMES[group as string]()}
				</button>
			{/each}
		</div>
	</div>
	<div class="min-w-[160px]">
		<label class="mb-2 block text-sm font-semibold text-amber-200">{m.type()}</label>
		<div class="flex flex-wrap gap-2">
			<button
				type="button"
				class={`rounded border px-3 py-1 text-sm font-medium transition-colors focus:ring-2 focus:ring-amber-400 focus:outline-none ${selectedTypes.length === 0 ? 'border-amber-500 bg-gradient-to-r from-yellow-300 to-amber-500 text-black shadow' : 'border-gray-700 bg-zinc-900 text-amber-200 hover:border-amber-400 hover:bg-amber-400/10'}`}
				onclick={() => (selectedTypes = [])}>{m.all()}</button
			>
			{#each VOD_TYPES as type}
				<button
					type="button"
					class={`rounded border px-3 py-1 text-sm font-medium transition-colors focus:ring-2 focus:ring-amber-400 focus:outline-none ${selectedTypes.includes(type) ? 'border-amber-500 bg-gradient-to-r from-yellow-300 to-amber-500 text-black shadow' : 'border-gray-700 bg-zinc-900 text-amber-200 hover:border-amber-400 hover:bg-amber-400/10'}`}
					onclick={() => (selectedTypes = toggleFilter(selectedTypes, type))}
					>{VOD_TYPES_LABELS[type]()}</button
				>
			{/each}
		</div>
	</div>
</div>
