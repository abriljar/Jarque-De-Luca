<!-- Script JS -->
<script>
	/* Importamos d3 para tenerlo disponible */
	import * as d3 from "d3";
	import mascotas from "/src/data/mascota_ideal.csv";

	console.log(mascotas);

	/* Dividimos 360 por la cantidad de elementos de la lista */
	let angle = 360 / mascotas.length;
	let radio = 340; /*sirve para agrandar el radio de los ovalos*/
	let radio2 = 250; /*radio del circulo interno*/
	let radio4 = 435;
	let numeros = ["1", "2", "3", "4", "5"];

	let especies = [...new Set(mascotas.map((m) => m.Familia))];
	console.log("especies", especies);

	/*info familias de animales*/
	let radio3 = 100; /*radio de las familias de animales*/
	let angle3 = 72;

	function getContrastColor(bgColor) {
		const color = d3.color(bgColor);
		if (color) {
			const luminance = 0.299 * color.r + 0.587 * color.g + 0.114 * color.b;
			return luminance > 128 ? "#000000" : "#FFFFFF";
		}
		return "#000000";
  	}
	const opacidadOvalos = d3
	.scaleLinear()
	.domain(["5","20"])
	.range(["0.1","1"])

	const tamanioOvalos = d3
	.scaleBand()
	.domain(["Grande","Mediana","Pequenia"])
	.range(["140","60"])

	const colorOvalos = d3
		.scaleOrdinal()
		.domain(["1","10","20","30"])
		.range(["#ff3333","#3342ff","#aa33ff","#ff33fa"]);

	const colorPunto = d3
		.scaleOrdinal()
		.domain(["Alta", "Baja", "Media"])
		.range(["#000000", "#FFFFFF", "grey"]);

	const imgEstrella = d3
		.scaleOrdinal()
		.domain(["20","50", "150", "450"])
		.range(["/images/Extra.png","/images/Grande.png","/images/Mediana.png","/images/Chica.png"]);

	const groupBySpecies = d3.rollup(
		mascotas,
		(v) => v.length,
		(d) => d.Familia,
	);
	console.log("group by species", groupBySpecies);
	//console.log(groupBySpecies.get("Perros"));

	function x(ang) {
		const rad = angToRad(ang);
		return Math.cos(rad) * radio * signX(ang);
	} /*funcion ovalos*/

	function y(ang) {
		const rad = angToRad(ang);
		return Math.sin(rad) * radio * signY(ang);
	} /*funcion ovalos*/

	function q(ang) {
		const rad = angToRad(ang);
		return Math.cos(rad) * radio4 * signX(ang);
	} /*funcion ovalos*/

	function h(ang) {
		const rad = angToRad(ang);
		return Math.sin(rad) * radio4 * signY(ang);
	} /*funcion ovalos*/

	function w(ang) {
		const rad = angToRad(ang);
		return Math.cos(rad) * radio2 * signX(ang);
	} /*funcion circulo interno*/

	function p(ang) {
		const rad = angToRad(ang);
		return Math.sin(rad) * radio2 * signY(ang);
	} /*funcion circulo interno*/

	function t(ang) {
		const rad = angToRad(ang);
		return Math.cos(rad) * radio3 * signX(ang);
	} /*funcion familias*/

	function v(ang) {
		const rad = angToRad(ang);
		return Math.sin(rad) * radio3 * signY(ang);
	} /*funcion familias*/

	function angToRad(ang) {
		return ang * (Math.PI / 180);
	}

	/* Corrección signo trigonométricas  */
	function signX(ang) {
		if (ang <= 90) return -1;
		if (ang <= 180) return -1;
		if (ang <= 270) return -1;
		return -1;
	}

	function signY(ang) {
		if (ang <= 90) return -1;
		if (ang <= 180) return -1;
		if (ang <= 270) return -1;
		return -1;
	}
</script>

<!-- Estructura contenido HTML -->
 
<svelte:head> 
	<title>Mascota ideal</title>

	<style>
	@import url('https://fonts.googleapis.com/css2?family=Fuzzy+Bubbles:wght@400;700&family=Roboto+Mono:ital,wght@0,100..700;1,100..700&display=swap');
	</style></svelte:head>

<div class="fondo">
	<div class="texto">
		<h1>Cómo elegir a tu mascota ideal</h1>
		<p>
			Elegir una mascota es una decisión importante, pero no tiene por qué
			ser complicada. En este gráfico encontrarás información sobre
			diferentes animales, desde perritos hasta conejitos, que se adaptan
			a distintos estilos de vida. ¿Tienes un hogar grande? ¿O tal vez
			vivís en un departamento? Acá podrás descubrir cuál es el compañero
			perfecto para vos, teniendo en cuenta su tamaño, necesidades de
			atención y hasta cuánta comida requieren al día. ¡Tu nuevo mejor
			amigo te está esperando!
		</p>
		<br>
		<img src="/images/referencias (1).png" alt="referencias">


	</div>
	<div class="chartContainer">
		<div class="container">
			<div id="circle" class="circle"></div>
			{#each mascotas as d, i}
				<div
					class="nombres"
					style:transform="
					translateX({q(angle * i)}px) translateY({h(angle * i)}px)
					
					"
					
				>
					<p>{d.Mascota}</p>
				</div>
			{/each}

			{#each mascotas as d, i}
				<div
					class="point"
					style:transform="
					translateX({x(angle * i)}px) translateY({y(angle * i)}px)
					rotate({angle * i + 90}deg)
					"
					style="
			background-color:{colorOvalos(d.Peso)};
			opacity:{opacidadOvalos(d.Expectativa)};
			height:{tamanioOvalos(d.Tamanio)}px;
			width:1.75vw
			"
				>
					
				</div>
			{/each}

			<div id="circle_interno" class="circle_interno"></div>
			{#each mascotas as d, i}
				<div
					class="container2"
					style:transform="
					translateX({w(angle * i)}px) translateY({p(angle * i)}px)
					"
				>
					<div class="icono">
						<img src={imgEstrella(d.Comida)} alt="icono1" />
					</div>
					<div
						class="circulito"
						style="background-color:{colorPunto(d.Atencion)};"
					><span class=tipo style="color: {getContrastColor(colorPunto(d.Atencion))};">{d.Tipo}</span></div>
				</div>
			{/each}

			<div id="familias" class="familias">
				<img src="/images/tipo (1).png" alt="tipo">
				
			</div>
			
		</div>
		

	</div>
</div>
<img class="Imagen" src="/images/ilustracion2.png" alt="referencias">
<footer>De Luca, Martín  |  Jarque, Abril Rocío</footer>
<!-- Estilos CSS -->

<style>
	

	h1{
		font-family: "Fuzzy Bubbles";
		font-weight: 700;
	}
	
	.nombres{
		font-family: "Fuzzy Bubbles";
		position: absolute;
	}
	.fondo {
		min-height: 100vh;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		max-width: 1280px;
		margin: auto;
	}
	.texto {
		text-align: center;
	}
	.container {
		position: relative;
		width: 100vw;
		height: 100vh;
		display: flex;
		justify-content: center;
		align-items: center; 
	}

	.circle,
	.circle_interno,
	.familias {
		position: absolute;
		border-radius: 50%;
	}

	.circle {
		width: 60vw;
		height: 60vw;
	}

	.circle_interno {
		width: 50vw;
		height: 50vw;
	}

	.familias {
		display: flex;
		position: relative;
		width: 12.5vw;
		height: 12.5vw;
		justify-content: center;
		align-items: center;
	}

	
	.point {
		position: absolute;
		border-radius: 50%;
		background: rgb(0, 0, 0);
	}

	.chartContainer {
		position: relative;
	}

	.circulito {
    display: flex;
    justify-content: center;
    align-items: center;
		position: absolute;
		height: 1vw;
		width: 1vw;
		border-radius: 50%;
		background: rgb(0, 0, 0);
	}

	.icono {
		height: 3vw;
		width: 3vw;
		position: absolute;
	}

	.container2 {
		position: absolute;
		display: flex;
		justify-content: center;
		align-items: center; 
		height: 3vw; 
		width: 3vw;
		z-index: 10;
	}
	.Imagen{
		display: block;
		width: 100%;
		height: auto;
		margin: 0;
	}
	
	footer{
		font-family: "Fuzzy Bubbles";
		letter-spacing: -0.5px;
		font-size: 15px;
		color: #000000;
		margin-top: 100px;
		display: flex;
		justify-content: center;
		align-items: center;
		background-color: #be8ea5;
		position: absolute; 
		left: 0;
		width: 100vw; 
		height: 15vh; 
		text-align: center;
		margin: 0;
	}
	
</style>
