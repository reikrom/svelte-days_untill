<script lang="ts">
	import { Button } from '$lib/components/ui/button';
	import { Calendar } from '$lib/components/ui/calendar';
	import CalendarIcon from 'lucide-svelte/icons/calendar';
	import cn from 'clsx';
	import * as Popover from '$lib/components/ui/popover';
	import { DateFormatter, type DateValue, getLocalTimeZone, today } from '@internationalized/date';
	import { goto } from '$app/navigation';

	const df = new DateFormatter('en-GB', {
		dateStyle: 'long'
	});

	let startDate: DateValue | undefined = undefined;
	let endDate: DateValue | undefined = undefined;

	function handleNavigation() {
		goto(`view?endDate=${endDate}&startDate=${startDate ? startDate : today(getLocalTimeZone())}`);
	}
</script>

<main class="container m-auto text-black dark:text-white">
	<section class="flex min-h-svh items-center justify-center justify-items-center">
		<div class="">
			<h1
				class="mb-12 text-3xl font-extrabold leading-snug text-white md:text-4xl lg:text-5xl xl:text-5xl 2xl:text-5xl"
			>
				Choose your end date
			</h1>
			<div class="flex flex-wrap gap-12">
				<Popover.Root>
					<Popover.Trigger asChild let:builder>
						<Button
							variant="outline"
							class={cn(
								'w-[280px] justify-start text-left font-bold',
							)}
							builders={[builder]}
						>
							<CalendarIcon class="mr-2 h-4 w-4" />
							{startDate ? df.format(startDate.toDate(getLocalTimeZone())) : 'Today'}
						</Button>
					</Popover.Trigger>
					<Popover.Content class="w-auto p-0">
						<Calendar bind:value={startDate} initialFocus />
					</Popover.Content>
				</Popover.Root>
				<Popover.Root>
					<Popover.Trigger asChild let:builder>
						<Button
							variant="outline"
							class={cn(
								'w-[280px] justify-start text-left font-normal',
								!endDate && 'text-muted-foreground'
							)}
							builders={[builder]}
						>
							<CalendarIcon class="mr-2 h-4 w-4" />
							{endDate ? df.format(endDate.toDate(getLocalTimeZone())) : 'Pick an end date'}
						</Button>
					</Popover.Trigger>
					<Popover.Content class="w-auto p-0">
						<Calendar bind:value={endDate} initialFocus />
					</Popover.Content>
				</Popover.Root>
			</div>
			<Button
        size='lg'
				onclick={handleNavigation}
				class={cn(
					'mt-6 font-bold  block bg-orange-500 hover:bg-orange-400 active:bg-orange-400 text-white',
					!endDate && 'hidden'
				)}>Confirm</Button
			>
		</div>
	</section>
</main>
