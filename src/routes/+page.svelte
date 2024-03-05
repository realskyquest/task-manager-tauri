<script lang="ts">
    import { BaseDirectory, exists, readTextFile, writeTextFile } from '@tauri-apps/api/fs'

	interface tasks_TYPE {
		title: string;
		content: string;
	}
	let tasks: Array<tasks_TYPE> = [];

	let sideBarOn: boolean = false;
	let title: string = '';
	let content: string = '';

    async function test() {
        const result = await exists('./saves/avatar.png', { dir: BaseDirectory.AppData });
        console.log(result)
    }

    test()
</script>

<section>
	<article class="prose mx-auto mt-4">
		<h1>Task Manager built with Tauri</h1>
	</article>

	<div class="divider mt-0"></div>

	<div
		class="mockup-window border bg-base-300 m-4"
		style="height: Calc(100dvh - 10rem); overflow-y: auto"
	>
		<div class="px-4 py-4 bg-base-200">
			{#key tasks}
				{#if tasks.length > 0}
					{#each tasks as task}
						<div class="collapse collapse-arrow bg-base-200">
							<input type="radio" name="my-accordion-2" />
							<div class="collapse-title text-xl font-medium">{task.title}</div>
							<div class="collapse-content">
								<pre class="border rounded-box bg-neutral-content">{task.content}</pre>
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
