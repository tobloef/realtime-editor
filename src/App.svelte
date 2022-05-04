<script lang="ts">
  import { Editor } from "@tiptap/core";
  import {
    onDestroy,
    onMount,
  } from "svelte";
  import StarterKit from "@tiptap/starter-kit";
  import { Collaboration } from "@tiptap/extension-collaboration";
  import CollaborationCursor from '@tiptap/extension-collaboration-cursor'
  import { HocuspocusProvider } from '@hocuspocus/provider'
  import { WebrtcProvider } from "y-webrtc";
  import * as Y from "yjs";
  import { IndexeddbPersistence } from "y-indexeddb";

  const documentId = "tobloef";
  const ydoc = new Y.Doc();

  const websocketProvider = new HocuspocusProvider({
    url: 'ws://127.0.0.1:1234',
    name: documentId,
    document: ydoc,
  });
  const indexeddbProvider = new IndexeddbPersistence(documentId, ydoc);

  let documentElement: HTMLDivElement;
  let editor: Editor;

  onMount(() => {
    editor = new Editor({
      element: documentElement,
      content: "",
      autofocus: true,
      onTransaction: () => {
        // Force re-render so `editor.isActive` works as expected
        editor = editor
      },
      extensions: [
        StarterKit.configure({
          history: false,
        }),
        Collaboration.configure({
          document: ydoc,
        }),
        CollaborationCursor.configure({
          provider: websocketProvider,
        }),
      ],
    })
  });

  onDestroy(() => {
    if (editor != null) {
      editor.destroy();
    }
  });
</script>

<div id="wrapper">
  <div id="editor">
    <div id="menu">
      {#if editor !== undefined}
        <button
          on:click={() => (
						editor.chain()
				      .focus()
		          .toggleHeading({level: 1})
				      .run()
		      )}
          class:active={editor.isActive('heading', { level: 1 })}
        >
          H1
        </button>
        <button
          on:click={() => (
						editor.chain()
				      .focus()
		          .toggleHeading({level: 2})
				      .run()
		      )}
          class:active={editor.isActive('heading', { level: 2 })}
        >
          H2
        </button>
        <button
          on:click={() => (
						editor.chain()
				      .focus()
		          .toggleHeading({level: 3})
				      .run()
		      )}
          class:active={editor.isActive('heading', { level: 3 })}
        >
          H3
        </button>
        <button
          on:click={() => (
						editor.chain()
				      .focus()
		          .toggleHeading({level: 4})
				      .run()
		      )}
          class:active={editor.isActive('heading', { level: 4 })}
        >
          H4
        </button>
        <button
          on:click={() => (
						editor.chain()
				      .focus()
				      .setParagraph()
				      .run()
		      )}
          class:active={editor.isActive('paragraph')}
        >
          P
        </button>
        <div
          style="padding-right: 10px; border-right: 1px solid grey; margin-right: 10px;"
        ></div>
        <button
          on:click={() => (
						editor.chain()
				      .focus()
				      .toggleBold()
				      .run()
		      )}
          class:active={editor.isActive('bold')}
        >
          B
        </button>
        <button
          on:click={() => (
						editor.chain()
				      .focus()
				      .toggleItalic()
				      .run()
		      )}
          class:active={editor.isActive('italic')}
        >
          I
        </button>
      {/if}
    </div>

    <div
      id="document"
      bind:this={documentElement}
    >
    </div>
  </div>
</div>

<style>
  :global(.ProseMirror-selectednode) {
    outline: 1px solid -webkit-focus-ring-color;
  }

  :global(.ProseMirror.ProseMirror-focused) {
    outline: none;
  }

  :global(input) {
    font-size: 1em;
  }

  :global(#document > .ProseMirror) {
    flex: 1;
    height: calc(100% - 10px * 2);
    width: calc(100% - 20px);
    min-width: 400px;
  }

  :global(html, body, #app) {
    height: 100%;
    margin: 0;
  }

  #wrapper {
    background: hsl(0, 0%, 95%);
    height: 100%;
    display: flex;
    justify-content: center;
    box-sizing: border-box;
    padding: 20px;
  }

  #editor {
    display: flex;
    flex-direction: column;
    height: 100%;
    width: 100%;
    max-width: 800px;
    box-shadow: rgba(60, 64, 67, 0.15) 0px 1px 3px 1px;
  }

  #menu {
    background: white;
    padding: 10px 20px;
    border-bottom: 1px solid lightgrey;
    display: flex;
  }

  #menu button + button {
    margin-left: 10px;
  }

  #menu button.active {
    background: black;
    color: white;
  }

  #document {
    flex: 1;
    box-sizing: border-box;
    background: white;
    height: 100%;
    overflow: auto;
    padding: 10px 0px 10px 20px;
    display: flex;
  }

  :global(.collaboration-cursor__caret) {
    border-left: 1px solid #0d0d0d;
    border-right: 1px solid #0d0d0d;
    margin-left: -1px;
    margin-right: -1px;
    pointer-events: none;
    position: relative;
    word-break: normal;
  }

  :global(.collaboration-cursor__label) {
    border-radius: 3px 3px 3px 0;
    color: #0d0d0d;
    font-size: 12px;
    font-style: normal;
    font-weight: 600;
    left: -1px;
    line-height: normal;
    padding: 0.1rem 0.3rem;
    position: absolute;
    top: -1.4em;
    user-select: none;
    white-space: nowrap;
  }
</style>
