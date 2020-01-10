<script>
  import { fade } from "svelte/transition";

  export let className;
  export let onClick = undefined;
  const isClickable = !!onClick;

  const handleClick = () => {
    onClick && onClick();
  };

  const fadeAbs = (node, { duration = 300 }) => {
    return {
      duration,
      css: t => {
        return `
          position: absolute;
          top: 50%;
          left: 50%;
          margin-top: -14px;
          margin-left: -14px;
          opacity: ${t};
          transform: scale(${t});
        `;
      }
    };
  };
</script>

<style>
  .icon {
    position: relative;
    display: block;
    width: 28px;
    height: 28px;
    border: 1px solid currentColor;
    border-radius: 50%;
    margin-left: auto;
    margin-right: auto;
    box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3), 0 0 4px rgba(0, 0, 0, 0.5) inset;
    background-color: rgba(255, 255, 255, 0.02);
    transition: transform 0.3s ease-out;
  }
  .icon--cancel::after,
  .icon--cancel::before {
    position: absolute;
    top: 50%;
    left: 50%;
    width: 16px;
    height: 1px;
    background-color: currentColor;
    content: "";
  }
  .icon--cancel::after {
    transform: translateX(-50%) rotate(45deg);
  }
  .icon--cancel::before {
    transform: translateX(-50%) rotate(-45deg);
  }
  .icon--check::after {
    position: absolute;
    top: 50%;
    left: 50%;
    width: 6px;
    height: 12px;
    margin-top: -1px;
    border-bottom: 1px solid currentColor;
    border-right: 1px solid currentColor;
    transform: translate(-50%, -50%) rotate(45deg);
    content: "";
  }
  .icon-wrapper {
    display: flex;
    flex-grow: 1;
    flex-direction: column;
    justify-content: center;
    padding: 12px 15px;
  }
  .icon-wrapper--clickable {
    cursor: pointer;
  }
  .icon-wrapper--clickable:hover .icon {
    transform: scale(1.3);
  }
  .icon--empty {
    opacity: 0.2;
  }
  .icon--empty::after {
    position: absolute;
    top: 50%;
    left: 50%;
    width: 14px;
    height: 1px;
    background-color: currentColor;
    transform: translateX(-50%);
    content: "";
  }
  .icon--green {
    color: #0dd332;
  }
  .icon--orange {
    color: #e56425;
  }
</style>

<div
  class="icon-wrapper {isClickable ? 'icon-wrapper--clickable' : ''}"
  on:click={handleClick}>
  <div in:fade out:fadeAbs class="icon {className ? className : ''}" />
</div>
