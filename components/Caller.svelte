<script>
  import Peer from "peerjs";
  import { onMount } from "svelte";

  export let id;
  const oppId = id === 1 ? 2 : 1;
  const peer = new Peer("arnu515s-godly-minecraft-com-" + id.toString());
  let connected = false;
  let oppConn = null;
  let initiator = false;
  let message = "";
  let selfStream = new MediaStream();
  let remoteStream = null;
  let selfVideo;

  peer.on("open", () => {
    console.info("Peer opened");

    peer.on("connection", conn => {
      if (connected) {
        conn.send("Already connected to a peer");
        conn.close();
        return;
      }
      conn.on("open", () => {
        console.log("got conn");
        connected = true;
        oppConn = conn;
        conn.on("data", data => console.log("recv", data));
        conn.send("test");
      });
    });
  });

  $: console.log(selfVideo);

  function call() {
    console.log("calling");
    let conn = peer.connect("arnu515s-godly-minecraft-com-" + oppId.toString());
    connected = !!conn;
    if (!connected) return alert("Failed to connect");
    oppConn = conn;
    initiator = true;
    conn.on("data", data => console.log("recv", data));
    conn.send("test");
  }

  function send() {
    console.log("send ");
    if (!connected || !message) return;

    oppConn.send(message.trim());
  }

  onMount(async () => {
    const stream = await navigator.mediaDevices.getUserMedia({
      video: true,
      audio: true
    });

    stream.getTracks().forEach(track => selfStream.addTrack);
  });
</script>

<div class="w3-container">
  <h1>{id}</h1>
  {#if !connected}
    <p><button on:click={call}>Call other peer</button></p>
  {:else}
    <input type="text" class="w3-input w3-border" bind:value={message}>
    <p>
    <button class="w3-button w3-blue" on:click={send}>Send</button></p>

    <div class="w3-margin w3-padding">
      <div style="width: 50%">
        <video bind:this={selfVideo}></video>
      </div>
    </div>
  {/if}
</div>
