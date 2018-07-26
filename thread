function getParameterDefinitions(){
	return [
		{name:'majorDiameter',  caption:'major diameter:',	type:'float',initial:59,	min:0.1,max:4200, step:0.1},
		{name:'pitch',          caption:'pitch:',           type:'float',initial:2.5,	min:0.1,max:420,  step:0.1},
		{name:'height',         caption:'height:',          type:'float',initial:20,	min:0.1,max:4200, step:0.1},
		{name:'holeDiameter',   caption:'hole diameter:',   type:'float',initial:50,	min:0,	max:420,  step:0.1},
		{name:'res',            caption:'resolution:',      type:'int',  initial:64,	min:32,	max:360,  step:1}
	];
}

function main(p){
  return thread(majorDiameter=p.majorDiameter, pitch=p.pitch, height=p.height, holeDiameter=p.holeDiameter, resolution=p.res);
}

/**
 * ISO metric external thread
 *
 * Let you build a ISO metric external thread or not ;)
 *
 * @author foxos
 *
 * @access public
 *
 * @link https://github.com/foxos42/parametric-thread
 *
 * @param {float}	[majorDiameter=47]	Dmaj -> outer Diameter of the thread -> Tolarance is made @ the external thread so choose it sligly smaler.
 * @param {float}	[pitch=2]			      P -> Pitch is the distance from the crest of one thread to the next.
 * @param {float}	[height=19]			    Height of the whole thread.
 * @param {float}	[holeDiameter=39]	  Insite hole to make it lighter or to let water flow through it. Set it to 0 if you do not want a hole.
 * @param {float}	[resolution=47]		  The resolution option determines the number of segments to create particular shapes.
 * 
 * @return {object} Shape of a thread.
 */
function thread(majorDiameter=47, pitch=1.5, height=19, holeDiameter=39, resolution=64){

	var threadAngle = 90; //60=ISO, 90=3d printer friendly
	var vshapeHeight = (1/tan(threadAngle/2)) * pitch; //H
	var minimalDiameter = majorDiameter-2 * (5/8) * vshapeHeight; //Dmin
	var truncatedWidthBottom = pitch/4;
	var truncatedWidthTop = pitch/8;
	var vshapeFlankWidth = (pitch - pitch/4 - pitch/8)/2;
	
	var hex = CSG.Polygon.createFromPoints([
			[0,                                 0,	0],
			[0,                                 0,	2*vshapeFlankWidth+truncatedWidthTop],
			[majorDiameter/2-minimalDiameter/2,	0,	vshapeFlankWidth+truncatedWidthTop],
			[majorDiameter/2-minimalDiameter/2,	0,	vshapeFlankWidth]
	]);
	
	var sliceEveryDegrees = 360/resolution;
	var loops = (height-pitch+truncatedWidthBottom)/pitch;
	var thread = hex.solidFromSlices({
		numslices: (360 * loops / sliceEveryDegrees) + 1,
		loop:false,
		callback: function(t, slice) {
			return this.translate([0, 0, pitch * loops * t]).rotate(
						[-minimalDiameter/2,0,0],
						[0, 0, 1],
						sliceEveryDegrees * slice
					);
		}
	});

	thread = union(
		thread.translate([minimalDiameter/2,0,0]),
		cylinder({d: minimalDiameter, h: height, fn: resolution})
	);
	
	if(holeDiameter!==0){
		thread = difference(
			thread,
			cylinder({d: holeDiameter, h: height, fn: resolution})
		);
	}
	
	return thread;
}
