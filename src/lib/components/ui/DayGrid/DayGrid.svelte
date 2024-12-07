<script lang="ts">
	import cn from 'clsx';
	import dayjs from 'dayjs';
	import weekday from 'dayjs/plugin/weekday';
	import { tooltip } from '../tooltip/tooltip';
	let { startDate, endDate, unit }: { startDate: string; endDate: string; unit: 'days' | 'weekend' } = $props();

	dayjs.extend(weekday);

	type Day = { date: string; past: boolean; today: boolean; dNum: number };

	const createDayArr = (start: string, end: string): Day[] => {
		const totalDays = dayjs(end).diff(start, 'days');
		const dayArr = Array(totalDays).fill('');
		const firstDayNumber = dayjs(startDate).day();

		return dayArr.map((_: unknown, index: number) => {
			let day = { date: '', past: false, today: false, dNum: 0 };
			day.dNum = (index + firstDayNumber) % 7;
			// @ts-expect-error - don't care
			day.date = dayjs(startDate).add(index + 1, 'day');
			day.past = dayjs().diff(dayjs(day.date), 'days') < 0;
			let hourDiff = Math.floor(dayjs().diff(dayjs(day.date), 'days', true));
			day.today = hourDiff + 1 == 0;

			return day;
		});
	};
	const createWeekArr = (days: Day[]): [Day][] => {
		// split days into weeks, each week starts with monday
		const weeks = [];
		// @ts-expect-error - don't care
		let week = [];
		days.forEach((day, i) => {
			if (day.dNum === 1 && i !== 0) {
				// @ts-expect-error - don't care
				weeks.push(week);
				week = [];
			}
			week.push(day);
		});
		// @ts-expect-error - don't care
		weeks.push(week);
		// @ts-expect-error - don't care
		return weeks;
	};
</script>

<div class="week flex flex-wrap gap-6 gap-y-10">
	{#if startDate}
		{#if unit === 'weekend'}
			{#each createWeekArr(createDayArr(startDate, endDate)) as weeks}
				<div class="week flex flex-col gap-3">
					{#each weeks as day}
						{#if dayjs(day.date).weekday() === 0 || dayjs(day.date).weekday() === 1}
							<div
								title={day.date}
								use:tooltip
								class={cn(
									'day life-square relative h-full  w-full rounded-full bg-black p-3',
									!day.past ? 'dark:bg-white/40' : 'dark:bg-white/70',
									!day.past && !day.today && 'cross',
									day.today && 'today'
								)}
							></div>
						{/if}
					{/each}
				</div>
			{/each}

		{:else}
			{#each createWeekArr(createDayArr(startDate, endDate)) as weeks}
				<div class="week flex flex-col gap-3">
					{#each weeks as day}
						<div
							title={day.date}
							use:tooltip
							class={cn(
								'day life-square relative h-full  w-full rounded-full bg-black p-3',
								!day.past ? 'dark:bg-white/40' : 'dark:bg-white/70',
								!day.past && !day.today && 'cross',
								day.today && 'today'
							)}
						></div>
					{/each}
				</div>
			{/each}
		{/if}
	{/if}
</div>

<style>
	.day {
		width: 10px;
		height: 10px;
	}
	.today {
		background: green;
		transform: scale(1);
		animation: pulse 1s infinite;
	}

	.cross:after {
		content: ' ';
		transform: rotate(45deg);
		height: 24px;
		width: 2px;
		position: absolute;
		background: #980202;
		top: 0;
	}
	.cross:before {
		content: ' ';
		transform: rotate(-45deg);
		height: 24px;
		width: 2px;
		position: absolute;
		background: #980202;
		top: 0;
	}

	@keyframes pulse {
		100% {
			transform: scale(1.5);
			opacity: 0.5;
		}
	}
</style>
