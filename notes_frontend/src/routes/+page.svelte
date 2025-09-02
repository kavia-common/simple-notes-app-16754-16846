<script lang="ts">
  import { notesStore } from '$lib/store/notes';
  import TopBar from '$lib/components/TopBar.svelte';
  import Sidebar from '$lib/components/Sidebar.svelte';
  import NoteView from '$lib/components/NoteView.svelte';
  import Fab from '$lib/components/Fab.svelte';

  import type { Note } from '$lib/store/notes';
  type NotesState = { notes: Note[]; selectedId: string | null };
  let state = $state<NotesState | undefined>(undefined);
  $effect.root(() => {
    const unsub = notesStore.subscribe((v) => (state = v));
    return unsub;
  });

  let isEditing = $state(false);

  function handleCreate() {
    notesStore.createNote({ title: 'Untitled note' });
    isEditing = true;
  }

  function handleSelect(id: string) {
    notesStore.selectNote(id);
    isEditing = false;
  }

  function handleSave(payload: { title: string; content: string }) {
    if (!state?.selectedId) return;
    notesStore.updateNote(state.selectedId, payload);
  }

  function handleToggleEdit() {
    if (!state?.selectedId) return;
    isEditing = !isEditing;
  }

  function handleDelete() {
    if (!state?.selectedId) return;
    const current = state.notes.find((n: Note) => n.id === state.selectedId);
    const ok = confirm(`Delete note "${current?.title || 'Untitled note'}"?`);
    if (ok) {
      notesStore.deleteNote(state.selectedId);
      isEditing = false;
    }
  }
</script>

<svelte:head>
  <title>Notes</title>
  <meta name="theme-color" content="#ffffff" />
</svelte:head>

<div class="app">
  <TopBar title="Notes" />
  <div class="content">
    <Sidebar
      notes={state?.notes ?? []}
      selectedId={state?.selectedId ?? null}
      onSelect={handleSelect}
    />
    <NoteView
      note={state?.notes.find((n: Note) => n.id === state?.selectedId) ?? null}
      editing={isEditing}
      onEditToggle={handleToggleEdit}
      onSave={handleSave}
      onDelete={handleDelete}
    />
  </div>
  <Fab onClick={handleCreate} />
</div>

<style>
  .app {
    display: flex;
    flex-direction: column;
    width: 100%;
  }
  .content {
    display: flex;
    min-height: calc(100vh - 56px);
    border-top: 1px solid rgba(0,0,0,0.04);
  }
</style>
