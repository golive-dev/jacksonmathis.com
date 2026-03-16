<div class="min-h-screen px-6 py-16 max-w-3xl mx-auto">
	<a href="/" class="text-xs transition-colors duration-200" style="color: var(--av-amber-dim);"
		onmouseenter={(e) => e.currentTarget.style.color = 'var(--av-amber)'}
		onmouseleave={(e) => e.currentTarget.style.color = 'var(--av-amber-dim)'}>
		← RETURN TO COCKPIT
	</a>

	<h1 class="text-4xl md:text-5xl font-bold mt-8 mb-2" style="font-family: 'Orbitron', sans-serif;">
		<span class="glow-amber" style="color: var(--av-amber);">ARCADE</span>
	</h1>
	<p class="text-xs mb-12" style="color: var(--av-amber-dim);">SELECT A SIMULATION</p>

	<div class="space-y-4">
		<a href="/games/flight"
			class="block instrument-panel p-6 transition-all duration-200 no-underline"
			onmouseenter={(e) => e.currentTarget.style.borderColor = 'var(--av-amber)'}
			onmouseleave={(e) => e.currentTarget.style.borderColor = 'var(--av-panel-border)'}>
			<div class="flex items-center gap-5">
				<span class="text-4xl">✈️</span>
				<div>
					<p class="text-[10px] tracking-widest" style="color: var(--av-amber-dim);">SIM-001</p>
					<h2 class="text-lg font-bold mt-0.5" style="color: var(--av-white);">Night Flight</h2>
					<p class="text-xs mt-1" style="color: var(--av-green-dim);">dodge the mountains. how far can you fly?</p>
				</div>
				<span class="ml-auto text-[10px] px-2 py-0.5 rounded"
					style="border: 1px solid var(--av-green-dim); color: var(--av-green);">ACTIVE</span>
			</div>
		</a>

		<a href="/games/field-goal"
			class="block instrument-panel p-6 transition-all duration-200 no-underline"
			onmouseenter={(e) => e.currentTarget.style.borderColor = 'var(--av-amber)'}
			onmouseleave={(e) => e.currentTarget.style.borderColor = 'var(--av-panel-border)'}>
			<div class="flex items-center gap-5">
				<span class="text-4xl">🏈</span>
				<div>
					<p class="text-[10px] tracking-widest" style="color: var(--av-amber-dim);">SIM-002</p>
					<h2 class="text-lg font-bold mt-0.5" style="color: var(--av-white);">Field Goal Kicker</h2>
					<p class="text-xs mt-1" style="color: var(--av-green-dim);">aim, power, kick. watch the wind.</p>
				</div>
				<span class="ml-auto text-[10px] px-2 py-0.5 rounded"
					style="border: 1px solid var(--av-green-dim); color: var(--av-green);">ACTIVE</span>
			</div>
		</a>
	</div>
</div>
