<script lang="ts">
	let bannerText: string = `

  __  __       _                                         _   _____ _
 |  \\/  |_   _| |__   __ _ _ __ ___  _ __ ___   __ _  __| | |  ___(_) __ _ ____
 | |\\/| | | | | '_ \\ / _\` | '_ \` _ \\| '_ \` _ \\ / _\` |/ _\` | | |_  | |/ _\` |_  /
 | |  | | |_| | | | | (_| | | | | | | | | | | | (_| | (_| | |  _| | | (_| |/ /
 |_|  |_|\\__,_|_| |_|\\__,_|_| |_| |_|_| |_| |_|\\__,_|\\__,_| |_|   |_|\\__,_/___|


    `;

	let commandHistory: { command: string; output: string; isError?: boolean }[] = [];
	let currentInput: string = "";
	let currentPath: string = "C:\\Users\\muhammad-fiaz"; // Default starting path

	// Supported commands
	const supportedCommands: Record<string, string> = {
		help: `Supported commands:
- help: Show this help message
- clear / cls: Clear the terminal
- about: Display information about the portfolio
- cd: Change directory (e.g., cd C:\\Users\\muhammad-fiaz).`,
		about: `To know more about me, please visit: https://muhammadfiaz.com`,
	};

	// Mock filesystem (for path validation)
	const mockFileSystem: string[] = [
		"C:\\Users\\muhammad-fiaz",
		"C:\\Users\\muhammad-fiaz\\Documents",
		"C:\\Users\\muhammad-fiaz\\Downloads",
		"C:\\"
	];

	// Process user input
	function executeCommand(command: string): void {
		// Split command into parts
		const parts = command.trim().split(" ");
		const cmd = parts[0].toLowerCase(); // First part is the command
		const args = parts.slice(1).join(" "); // Remaining parts are arguments

		switch (cmd) {
			case "help":
				commandHistory = [
					...commandHistory,
					{ command, output: supportedCommands["help"], isError: false },
				];
				break;

			case "clear":
			case "cls":
				commandHistory = []; // Clear history
				break;

			case "about":
				commandHistory = [
					...commandHistory,
					{ command, output: supportedCommands["about"], isError: false },
				];
				break;

			case "cd":
				handleCdCommand(args, command);
				break;

			default:
				// Handle unknown commands
				commandHistory = [
					...commandHistory,
					{
						command,
						output: `'${command}' is not recognized as the name of a cmdlet, function, script file, or operable program. Type 'help' for more information.`,
						isError: true,
					},
				];
		}
		currentInput = ""; // Clear the input after execution
	}

	// Handle the 'cd' command
	function handleCdCommand(path: string, fullCommand: string): void {
		if (!path) {
			commandHistory = [
				...commandHistory,
				{ command: fullCommand, output: "The system cannot find the path specified.", isError: true },
			];
			return;
		}

		// Normalize path (e.g., replace `C://` with `C:\`)
		const normalizedPath = path.replace(/\//g, "\\");

		// Check if the path exists in the mock filesystem
		if (mockFileSystem.includes(normalizedPath)) {
			currentPath = normalizedPath; // Update the current path
			commandHistory = [
				...commandHistory,
				{
					command: fullCommand,
					output: `Changed directory to ${normalizedPath}`,
					isError: false,
				},
			];
		} else {
			commandHistory = [
				...commandHistory,
				{
					command: fullCommand,
					output: `The system cannot find the path specified: ${normalizedPath}`,
					isError: true,
				},
			];
		}
	}
</script>

<div class="flex flex-col h-screen bg-[var(--terminal-black)] text-[var(--terminal-green)] font-monospace p-4">
	<!-- ASCII Banner -->
	<pre class="text-[var(--terminal-yellow)]">{bannerText}</pre>
	<h2 class="text-[var(--terminal-yellow)]">Type 'help' to see list of available commands.</h2>

	<!-- Command History -->
	{#each commandHistory as { command, output, isError } (command) }
		<div class="mb-2">
			<!-- Command -->
			<div class="flex">
				<span class="text-[var(--terminal-green)]">PS {currentPath} ></span>
				<span class="ml-2 text-[var(--terminal-green)]">{command}</span>
			</div>
			<!-- Output -->
			<div
				class="whitespace-pre-wrap"
				class:text-[var(--terminal-yellow)]={!isError}
				class:text-[var(--terminal-red)]={isError}
			>
				{output}
			</div>
		</div>
	{/each}

	<!-- Current Input (always on the same line as the prompt) -->
	<div class="flex items-center">
		<span class="text-[var(--terminal-green)]">PS {currentPath} ></span>
		<input
			bind:value={currentInput}
			on:keydown={(e) => {
				if (e.key === "Enter" && currentInput.trim()) {
					executeCommand(currentInput.trim());
				}
			}}
			class="input-terminal flex-1 ml-2 h-6"

		/>
	</div>
</div>

<style>
    /* General input styles */
    .input-terminal {
        background: transparent;
        color: var(--terminal-green);
        border: none;
        outline: none;
        height: auto;
    }

    /* Ensure no border or outline on hover and focus */
    .input-terminal:hover,
    .input-terminal:focus {
        background: transparent;
        border: none;
        outline: none;
        box-shadow: none; /* Ensures no shadow effect */
    }

    /* Terminal color variables */
    :root {
        --terminal-black: #1e1e1e;
        --terminal-green: #00ff00;
        --terminal-yellow: #ffff00;
        --terminal-red: #ff0000;
    }
</style>