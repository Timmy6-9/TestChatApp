<script>

	const ws = new WebSocket('ws://192.168.0.220:443/');

    let username;
    let usernameField;
	let userClosedSession = false;

	// Remember to add reconnect function

	let msgBoxObj;
	let msgDiv;
	let scrollCheck;

	let count = 0;
	let msgContent = "";

	function connect(){
		const ws = new WebSocket('ws://192.168.0.220:443/');
	};

	ws.onopen = function() {
		console.log('Connected!');
	};

	ws.onclose = function () {
		if(!userClosedSession){
			console.log("Attempting to reconnect");
			setTimeout(connect, 3000);
		}
		else{
			console.log('Connection closed');
		}
    };

    ws.onmessage = function (event) {
		console.log(event.data);
		const currentMsg = event.data;
		addMsg(currentMsg);
    };

    ws.onerror = function () {
        console.log('Error!');
    };

	function setUser(){
		username = usernameField;
		const user = {
			type: 'username',
			user: username
		}
		console.log(user);
		ws.send(JSON.stringify(user));
	};

	function addMsg(cMsg){
		const checkUser = cMsg.split(":")
		//const lBreak = document.createElement('br');
		const msgToAdd = document.createElement('span');
		msgToAdd.textContent = cMsg;
		if(checkUser[0] != username){
			msgToAdd.style.backgroundColor = "pink";
			msgToAdd.style.borderColor = "pink";
		}
		else{
			msgToAdd.style.backgroundColor = "greenyellow";
			msgToAdd.style.borderColor = "greenyellow";
		}
		msgToAdd.style.marginTop = "0.35%";
		msgToAdd.style.marginLeft = "0.25%";
		msgToAdd.style.padding = "0.15vw";
		msgToAdd.style.width = "fit-content";
		msgToAdd.style.borderStyle = "outset";
		msgDiv.appendChild(msgToAdd);
		if(!scrollCheck){
			msgBoxObj.scrollTo(0, document.body.scrollHeight);
		}
	};

	function sendMsg(){
		const msg = {
			type: 'message',
			user: username,
			text: msgContent,
			id: count,
			time: Date.now()
		};
		console.log(msg);
		ws.send(JSON.stringify(msg));
		count++;
		msgContent = "";
	};

</script>

{#if !username}

   <h2> Insert Username: </h2>

   <input bind:value={usernameField}> <button on:click={setUser}> Confirm </button>

{/if}

{#if username}

	<messageBox bind:this={msgBoxObj}>
		<messages bind:this={msgDiv}>
	</messageBox>

	<postBoxContent>
		<input bind:value={msgContent}> <button on:click={sendMsg}> Send </button>
		<br>
		Disable Auto-Scroll: <input type=checkbox bind:checked={scrollCheck}>
	</postBoxContent>
{/if}

<style>

    h2{
        text-decoration: underline;
    }
	messageBox{
		display: block;
		margin: 1%;
		margin-left: 0%;
		border: 5% solid black;
		background-color: aliceblue;
		overflow: scroll;
		height: 70vh;
		width: 100vw;
	}
	messages{
		display: flex;
		flex-direction: column;
		justify-content: top;
		color: black;
		font-size: 110%;
		filter: drop-shadow(3px 3px 1px black);
		font-family: custom;
	}
	button{
		border-radius: 7.5%;
		font-family: custom;
	}
</style>
