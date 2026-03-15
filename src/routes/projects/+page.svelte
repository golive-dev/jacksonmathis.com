<script lang="ts">
	const projects = [
		{
			name: 'This Website',
			desc: 'My first real web project. Built with SvelteKit + Tailwind.',
			tech: ['Svelte', 'TypeScript', 'CSS'],
			status: 'live',
			emoji: '🌐',
		},
		{
			name: 'Coming Soon',
			desc: 'More projects loading... check back.',
			tech: ['???'],
			status: 'wip',
			emoji: '🔧',
		},
	];
</script>

<div class="min-h-screen px-6 py-20 max-w-2xl mx-auto">
	<a href="/" class="font-mono text-xs text-gray-600 hover:text-neon-green transition-colors">← back</a>

	<h1 class="font-[family-name:var(--font-family-display)] text-4xl md:text-5xl font-bold mt-8 mb-2">
		<span class="text-neon-blue glow-blue">/projects</span>
	</h1>
	<p class="text-gray-500 font-mono text-sm mb-12">things i've built or am building</p>

	<div class="space-y-6">
		{#each projects as project}
			<div class="group bg-dark-card border border-dark-border rounded-lg p-6 hover:border-neon-blue/30 transition-all duration-300">
				<div class="flex items-start justify-between">
					<div>
						<h2 class="text-lg font-bold text-white group-hover:text-neon-blue transition-colors">
							{project.emoji} {project.name}
						</h2>
						<p class="text-gray-400 text-sm mt-1">{project.desc}</p>
					</div>
					<span class="font-mono text-xs px-2 py-0.5 rounded-full {project.status === 'live' ? 'text-neon-green border border-neon-green/30' : 'text-yellow-500 border border-yellow-500/30'}">
						{project.status}
					</span>
				</div>
				<div class="flex gap-2 mt-4">
					{#each project.tech as t}
						<span class="font-mono text-xs text-gray-500 bg-dark border border-dark-border rounded px-2 py-0.5">{t}</span>
					{/each}
				</div>
			</div>
		{/each}
	</div>
</div>
