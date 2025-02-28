<script lang="ts">
	import { cubicOut } from 'svelte/easing';
	import { getChartfullContext } from 'graficos/chartfull';
	import { classNames } from '$lib/utils';
	import Axis from './Axis.svelte';
	import Tick from './Tick.svelte';
	import type { Scale } from './types';
	import type { AxisContextProps } from './context';
	import type { Writable } from 'svelte/store';

	const { innerWidth$ } = getChartfullContext();

	export let scale: Scale;
	export let x = 0;
	export let y = 0;
	export let orient: 'top' | 'bottom' = 'bottom';
	export let tickArguments: unknown[] = [10];
	export let tickValues: unknown[] | undefined = undefined;
	export let tickFormat: ((tick: string | number | Date) => string) | undefined = undefined;

	export let tickSize = 6;
	export let tickPadding = 8;

	export let fontFamily: string | undefined = undefined;
	export let fontSize: string | undefined = undefined;
	export let fontSizeAdjust: string | undefined = undefined;
	export let fontStretch: string | undefined = undefined;
	export let fontStyle: string | undefined = undefined;
	export let fontVariant: string | undefined = undefined;
	export let fontWeight: string | undefined = undefined;

	export let fill = 'rgba(0 0 0 / .4)';

	export let stroke: string | undefined = 'rgba(0 0 0/ .5)';
	export let strokeWidth: string | undefined = '';
	export let strokeOpacity: string | undefined = '';
	export let strokeLinecap: 'inherit' | 'round' | 'butt' | 'square' | null | undefined = undefined;
	export let strokeLinejoin: 'inherit' | 'round' | 'miter' | 'bevel' | null | undefined = undefined;
	export let strokeDasharray: string | undefined = '';
	export let strokeDashoffset: string | undefined = '';
	export let strokeMiterlimit: string | undefined = '';

	export let textAnchor: 'start' | 'middle' | 'end' = 'middle';
	export let d: string | undefined = undefined;

	export let duration = 100;
	export let delay = 0;
	export let easing = cubicOut;

	let _class = '';
	export { _class as class };

	let context: Writable<AxisContextProps>;
	$: if (context) {
		context.update((val) => ({
			...val,
			domain: {
				stroke,
				strokeWidth,
				strokeOpacity,
				strokeLinecap,
				strokeLinejoin,
				strokeDasharray,
				strokeDashoffset,
				strokeMiterlimit
			}
		}));
	}

	const k = orient === 'top' ? -1 : 1;

	const isPathDataSet = !!d;

	function canRender(scale: Scale) {
		console.log(scale.domain());
		return scale.domain().every(Boolean);
	}
</script>

<Axis
	{scale}
	{x}
	{y}
	{orient}
	{tickPadding}
	{fontFamily}
	{fontWeight}
	{fontSize}
	{fontSizeAdjust}
	{fontVariant}
	{fontStyle}
	{fontStretch}
	{textAnchor}
	{fill}
	{tickValues}
	{tickArguments}
	{tickSize}
	{duration}
	{delay}
	{easing}
	{tickFormat}
	class={classNames(_class, 'x', orient)}
	bind:context
	let:ticks
	let:tickFormat
>
	{#each ticks as tick, index (tick + '')}
		<slot {index} {tick} {tickFormat}>
			<Tick {tick} />
		</slot>
	{/each}

	{@const d = `M0,${k * 6}V0H${$innerWidth$}V${k * 6}`}

	<slot name="domain" {d}>
		<path
			class="domain"
			fill="none"
			{stroke}
			stroke-width={strokeWidth}
			stroke-opacity={strokeOpacity}
			stroke-linecap={strokeLinecap}
			stroke-linejoin={strokeLinejoin}
			stroke-dasharray={strokeDasharray}
			stroke-dashoffset={strokeDashoffset}
			stroke-miterlimit={strokeMiterlimit}
			{d}
		/>
	</slot>

	<g class="label" id="x-axis-label">
		<slot name="label" />
	</g>
</Axis>
