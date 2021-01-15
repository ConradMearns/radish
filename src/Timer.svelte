<script lang="ts">

const states = {
	READY: 'ready',
	COUNTDOWN: 'countdown',
	COUNTDOWN_END: 'countdown_end',
}

let state = states.READY;

let c = -1;

let task = '';

let timer_init = 20;

setInterval(() => {
	if (state == states.COUNTDOWN) {
		if (c > 0) c--;
		if (c == 0) handleEnd();
	}
	if (state == states.COUNTDOWN_END) {
		c++;
	}
}, 1000);

$: minutes = Math.floor(c / 60);
$: minname = minutes > 1 ? "mins" : "min";
$: seconds = Math.floor(c - minutes * 60)

function handleStart() {
	c = timer_init * 60;
	console.log("started at", new Date());
}

function handleEnd() {
	if (state == states.COUNTDOWN) {
		console.log('timer ended at', new Date());
		console.log('break started at', new Date());
		c = -1;
		state = states.COUNTDOWN_END;
		sendNotification();
	}
}

function handleOnReady() {
	if (state == states.READY) {
		state = states.COUNTDOWN;
		handleStart();
	}	
}

function handleCancel() {
	if (state == states.COUNTDOWN) {
		state = states.READY;
		console.log('canceled at', new Date());
	}	
}

function handleFinish() {
	if (state == states.COUNTDOWN_END) {
		state = states.READY;
		console.log('finished break at', new Date());
	}	
}

function handleNotificationAccess() {
	Notification.requestPermission().then(function(result) {
        if(result === 'granted') {
            randomNotification();
        }
    });
}

function randomNotification() {
    var notifTitle = 'Thanks';
    var notifBody = 'This is what the notifcation will look like';
	var notifImg = 'radish.svg';
    var options = {
        body: notifBody,
        icon: notifImg
    }
    var notif = new Notification(notifTitle, options);
	
	var snd = new Audio('/blip.mp3');
	snd.play();
}

function sendNotification() {
    var notifTitle = 'time block for ' + task + ' complete';
    // var notifBody = 'LINE\nLINE\nLINE';
    // var notifBody = 'time block for ' + task + ' complete';
    var notifImg = 'radish.svg';
    var options = {
        // body: notifBody,
        icon: notifImg
    }
    var notif = new Notification(notifTitle, options);
	
	var snd = new Audio('/blip.mp3');
	snd.play();
}

</script>








<button on:click={handleNotificationAccess}>Request Notification Access</button>




<pre>
State: {state}
{#if state==states.READY}
Task: <input bind:value={task} placeholder="Task / Habit">
Minutes: <input bind:value={timer_init} placeholder="x">
{:else}
Task: {task}
{/if}
</pre>



{#if state==states.READY}
<button on:click={handleOnReady}>Let's Begin</button>
{/if}





{#if state==states.COUNTDOWN}
{minutes}:{(seconds > 9 ? seconds : "0"+seconds)}
<button on:click={handleCancel}>Cancel</button>
{/if}




{#if state==states.COUNTDOWN_END}

+ {minutes}:{(seconds > 9 ? seconds : "0"+seconds)}
<button on:click={handleFinish}>End Break</button>

{/if}