<script lang="ts">
export let guessedStatuses: Record<string, 'correct'|'present'|'absent'> = {};
export let onKeyPress: (key: string) => void;
export let guessEnabled: boolean = true;
export let onGuess: () => void;
export let guessLabel: string = 'Guess';
export let backspaceLabel: string = 'Backspace';

const rows = [
  ['Q', 'W', 'E', 'R', 'T', 'Y', 'U', 'I', 'O', 'P', 'Backspace'],
  ['A', 'S', 'D', 'F', 'G', 'H', 'J', 'K', 'L', 'Ö', 'Ä'],
  ['Z', 'X', 'C', 'V', 'B', 'N', 'M', 'Guess']
];

function handleKeydown(event: KeyboardEvent) {
  if (event.metaKey || event.ctrlKey || event.altKey) return;

  const target = event.target as HTMLElement;
  if (target.tagName === 'INPUT' || target.tagName === 'TEXTAREA' || target.isContentEditable) return;

  const key = event.key.toUpperCase();

  if (key === 'ENTER') {
    event.preventDefault();
    if (!event.repeat && guessEnabled) {
      onGuess();
    }
    return;
  }

  if (key === 'BACKSPACE') {
    event.preventDefault();
    onKeyPress('Backspace');
    return;
  }

  for (const row of rows) {
    if (row.includes(key) && key !== 'Guess' && key !== 'Backspace') {
      event.preventDefault();
      onKeyPress(key);
      return;
    }
  }
}
</script>

<svelte:window on:keydown={handleKeydown} />

<div class="w-full max-w-md flex flex-col items-center mt-4 gap-2 mx-auto px-1 sm:px-2 touch-manipulation">
  <div class="flex flex-col gap-2 w-full">
    {#each rows as row, rowIdx (rowIdx)}
      <div class="flex gap-1 w-full">
        {#if rowIdx === 1}
          <div class="flex-[0.1]"></div>
        {/if}
        {#if rowIdx === 2}
          <div class="flex-[0.3]"></div>
        {/if}

        {#each row as key (key)}
        {#if key === 'Guess'}
          <button
            class="flex-[3] h-10 sm:h-12 border border-blue-700 dark:border-blue-900 rounded font-bold text-base sm:text-lg bg-blue-600 dark:bg-blue-800 text-white px-2 py-2 disabled:opacity-50 min-w-0"
            on:click={onGuess}
            disabled={!guessEnabled}
            aria-label={guessLabel}
          >{guessLabel}</button>
        {:else if key === 'Backspace'}
          <button
            class="flex-[1.5] h-10 sm:h-12 border border-red-700 dark:border-red-900 rounded font-bold text-base sm:text-lg bg-red-500 dark:bg-red-700 text-white min-w-0"
            on:click={() => onKeyPress('Backspace')}
            aria-label={backspaceLabel}
          >
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" width="24" height="24" class="mx-auto">
              <path d="M6.707 17.293A1 1 0 0 1 6.293 16.707L2.293 12.707A1 1 0 0 1 2.293 11.293L6.293 7.293A1 1 0 0 1 7.707 7.293L8.707 8.293A1 1 0 0 1 8.707 9.707L6.414 12L8.707 14.293A1 1 0 0 1 8.707 15.707L7.707 16.707A1 1 0 0 1 6.707 17.293ZM21 7A1 1 0 0 1 22 8V16A1 1 0 0 1 21 17H10A1 1 0 0 1 9 16V8A1 1 0 0 1 10 7H21ZM20 9H11V15H20V9Z" />
            </svg>
          </button>
        {:else}
          <button
            class={
              `flex-1 h-10 sm:h-12 border rounded font-bold text-base sm:text-lg transition-colors duration-100 min-w-0 ` +
              (guessedStatuses[key] === 'correct'
                ? 'bg-green-500 text-white border-green-600 dark:border-green-700'
                : guessedStatuses[key] === 'present'
                ? 'bg-orange-400 text-white border-orange-500 dark:border-orange-700'
                : guessedStatuses[key] === 'absent'
                ? 'bg-gray-400 text-gray-700 border-gray-500 dark:bg-gray-700 dark:text-gray-400 dark:border-gray-800'
                : 'bg-gray-100 text-gray-900 border-gray-400 dark:bg-gray-800 dark:text-gray-100 dark:border-gray-700')
            }
            on:click={() => onKeyPress(key)}
            aria-label={key}
          >{key}</button>
        {/if}
      {/each}
      </div>
    {/each}
  </div>
</div>
