<script>
	import Tailwindcss from './Tailwindcss.svelte';
	import anime from 'animejs';
	import { onMount,onDestroy } from 'svelte';
	let start=false;
	$:{
		anime({
			targets:'.btn',
			translateY:start?180:0,
		});
	}
	var styles=[];
	var temp=[];
	var move=0;
	for(let i=0;i<49;i++)
	{
		styles.push({
			dimension:Math.floor(1+Math.random()*50).toString()+"px",
			opacity:Math.random().toString() ,
			id:"block"+i.toString()

		})
		temp.push(0);
	}
	onMount(() =>{
		for(let i=0;i<49;i++)
	anime({
		targets:"."+styles[i].id,
		translateY:Math.floor(Math.random()*screen.height),
		translateX:Math.floor(Math.random()*screen.width),
		duration:5000,
		easing:'linear',
		
	});
	});
	const interval = setInterval(() => {
		if(!start)
		{
			for(let i=0;i<49;i++)
	anime({
		targets:"."+styles[i].id,
		translateY:Math.floor(Math.random()*screen.height),
		translateX:Math.floor(Math.random()*screen.width),
		duration:5000,
		easing:'linear',

	});
		}
	}, 5000);
	function game(){start=!start;
		if(start)
		{
			for(let i=0;i<49;i++)
			anime({
		targets:"."+styles[i].id,
		width:"50px",
		height:"50px",
		translateY:Math.floor(screen.height/2)-320+Math.floor(i/7)*60,
		translateX:Math.floor(screen.width/2)-220+(i%7)*60,
		opacity:1,
		easing:'easeOutElastic',
		duration:5000,
		

	});
		}
		else{
			move=0;
			for(let i=0;i<49;i++){
	anime({
		targets:"."+styles[i].id,
		translateY:Math.floor(Math.random()*screen.height),
		translateX:Math.floor(Math.random()*screen.width),
		width:styles[i].dimension,
		height:styles[i].dimension,
		backgroundColor: 'rgb(236, 242, 223)',
		opacity:styles[i].opacity,
		duration:5000,
		easing:'linear'
		

	});temp[i]=0;}
		}
	}
	function handleMove(i) {
		if(!start)return;
		let col=i%7;
		let endat=-1;
		for(let j=0;j<7;j++)
		{
			if(temp[j*7+col]==0)
			{
				endat=j;
				anime({
					targets:"."+styles[j*7+col].id,
					backgroundColor:move%2==0?"rgb(170, 222, 40)":"rgb(19, 159, 214)",
					duration:1000,
				});
				if(j>0)
				{
					anime({
						targets:"."+styles[(j-1)*7+col].id,
						backgroundColor:["rgb(252, 207, 3)","rgb(236, 242, 223)"],
						duration:1000,
					});
				}
			}
			else
			{
				break;
			}
		}
		if(endat!=-1)
		{
			
			temp[endat*7+col]=(move%2)+1;
			move+=1;
			if(checkWin(i))
			{
				for(let i=0;i<49;i++)
				{
					anime({
					targets:"."+styles[i].id,
					backgroundColor:move%2==1?"rgb(170, 222, 40)":"rgb(19, 159, 214)",
					duration:3000,
				});
				
				}
				setTimeout(() => { game(); },1000);
			}
		}
	}
	function checkWin(i)
	{
		let row=Math.floor(i/7);
		let col=i%7;
		var left=0;
		var right=0;
		var top=0;
		var down=0;
		for(let j=col-1;j>=0;j--)
		{
			if(temp[row*7+j]==temp[i])
			{
				left+=1;
			}
			else {
				break;
			}
		}
		for(let j=col+1;j<7;j++)
		{
			if(temp[row*7+j]==temp[i])
			{
				right+=1;
			}
			else {
				break;
			}
		}
		for(let j=row+1;j<7;j++)
		{
			if(temp[j*7+col]==temp[i])
			{
				down+=1;
			}
			else
			{
				break;
			}
		}
		for(let j=row-1;j>=0;j--)
		{
			if(temp[j*7+col]==temp[i])
			{
				top+=1;
			}
			else
			{
				break;
			}
		}
		if(left+right+1>=4)return true;
		if(top+down+1>=4)return true;
		return false;
	}

	onDestroy(() => clearInterval(interval));
</script>

<Tailwindcss />
<main>
	
	<div class="  h-screen w-screen bg-gradient-to-bl from-red-600 to-purple-500  relative overflow-hidden">
		<div class=" btn h-10 w-24 bg-yellow-300 text-center py-2 font-extrabold rounded-md  absolute  cursor-pointer hover:bg-yellow-100" on:click={game}>{start?"END":"START"}</div>
		{#each styles as sty,i}
			<div class="{sty.id} rounded-full absolute bg-white " style="height: {sty.dimension};width:{sty.dimension};opacity:{sty.opacity};cursor:{start?"pointer":"default"}" on:click={() => handleMove(i)}></div>
		{/each}
	</div>
	
	
</main>

<style>
	.btn
	{
		left: 45vw;
		top:50vh;
	}
</style>
