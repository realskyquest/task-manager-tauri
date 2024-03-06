<script lang="ts">
	import { appLocalDataDir } from '@tauri-apps/api/path';
	import {
		BaseDirectory,
		exists,
		readTextFile,
		writeTextFile,
		createDir
	} from '@tauri-apps/api/fs';
	import { onMount } from 'svelte';

	interface tasks_TYPE {
		title: string;
		content: string;
	}
	let tasks: Array<tasks_TYPE> = [];

	let sideBarOn: boolean = false;
	let title: string = '';
	let content: string = '';

	onMount(async () => {
		const appLocalDataDirPath = await appLocalDataDir();

		const saveExists = await exists(`${appLocalDataDirPath}saves\\file.json`, {
			dir: BaseDirectory.AppData
		});
		if (saveExists === true) {
			const read = await readTextFile(`${appLocalDataDirPath}saves\\file.json`, {
				dir: BaseDirectory.AppData
			});
			tasks = JSON.parse(read);
		}
	});

	async function handleSave() {
		const appLocalDataDirPath = await appLocalDataDir();

		const fileExists = await exists(`${appLocalDataDirPath}saves`, { dir: BaseDirectory.AppData });

		if (fileExists === false) {
			await createDir(`${appLocalDataDirPath}saves`, { dir: BaseDirectory.AppData });
		}

		await writeTextFile(`${appLocalDataDirPath}saves\\file.json`, JSON.stringify(tasks), {
			dir: BaseDirectory.AppData
		});
	}

	$: {
		if (tasks.length > 0) {
			handleSave();
		}
	}
</script>

<section>
	<article class="prose mx-auto mt-4">
		<h1>Task Manager built with Tauri (demo)</h1>
	</article>

	<div class="divider mt-0"></div>

	<div class="mockup-window border bg-base-300 m-4">
		<div class="px-4 py-4 bg-base-200" style="height: Calc(100dvh - 14rem); overflow-y: auto">
			{#key tasks}
				{#if tasks.length > 0}
					{#each tasks as task, taskIndex}
						<div class="collapse collapse-arrow bg-info mt-2 mb-2 glass">
							<input type="checkbox" />
							<div class="collapse-title text-xl font-medium">{task.title}</div>
							<div class="collapse-content bg-neutral-content">
								<button
									class="btn btn-error btn-sm btn-circle btn-outline btn-ghost absolute right-2 mt-1"
									on:click={() => {
										// Remove task from tasks
										tasks.splice(taskIndex, 1);
										tasks = tasks;

										// Save tasks
										handleSave();
									}}>✕</button
								>
								<pre>{task.content}</pre>
							</div>
						</div>
					{/each}
				{/if}
			{/key}
		</div>
	</div>
</section>

<div class="drawer z-[100]">
	<input id="my-drawer" type="checkbox" class="drawer-toggle" bind:checked={sideBarOn} />
	<div class="drawer-content">
		<!-- Page content here -->
		<label for="my-drawer" class="btn btn-primary drawer-button ml-4">Add new task</label>
		<button class="btn btn-error" on:click={() => {tasks = []; handleSave()}}>Clear all</button>
	</div>
	<div class="drawer-side">
		<label for="my-drawer" aria-label="close sidebar" class="drawer-overlay"></label>
		<ul class="menu p-4 w-80 min-h-full bg-base-200 text-base-content">
			<!-- Sidebar content here -->
			<form
				on:submit|preventDefault={() => {
					sideBarOn = false;
					tasks.push({ title: title, content: content });
					tasks = tasks;
					title = '';
					content = '';
				}}
			>
				<fieldset>
					<label>
						Title
						<input
							bind:value={title}
							type="text"
							placeholder="Type [Title] here"
							class="input input-bordered w-full max-w-xs"
							name="task_manager_title"
							required
						/>
					</label>
					<textarea
						bind:value={content}
						class="textarea textarea-bordered mt-4"
						placeholder="Type [Content] here"
						name="task_manager_content"
						required
					></textarea>
				</fieldset>
				<input type="submit" value="Submit" class="btn btn-secondary mt-4" />
			</form>
		</ul>
	</div>
</div>
