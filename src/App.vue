<script setup>
import { ref } from 'vue';
Number.prototype.numberOfCharactersBeforeAndAfter = function () {
	const str = this.toString();

	if (str.indexOf('e') === -1) {
		const result = str.split('.').map(i => i.length);
		return {
			before: result[0],
			after: result[1] || 0,
		};
	}

	const exponent = parseInt(str.split('-')[1], 10);
	const result = this.toFixed(exponent)
		.split('.')
		.map(i => i.length);
	return {
		before: result[0],
		after: result[1] || 0,
	};
};

const calculated = ref('0');
const currentMode = ref('');
const input = ref('');

const clickNumber = number => {
	if (input.value == '0' && number == 0) return;
	if (input.value.length > 9) return;
	input.value += number;
};
const clickEquals = () => {
	if (currentMode.value == 'division') {
		calculated.value = Number(calculated.value) / Number(input.value);
		input.value = '';
	}
	if (currentMode.value == 'multiplication') {
		calculated.value = Number(calculated.value) * Number(input.value);
		input.value = '';
	}
	if (currentMode.value == 'subtraction') {
		calculated.value = Number(calculated.value) - Number(input.value);
		input.value = '';
	}
	if (currentMode.value == 'addition') {
		calculated.value = Number(calculated.value) + Number(input.value);
		input.value = '';
	}
	currentMode.value = '';
	if (Number.isNaN(calculated.value)) {
		calculated.value = 'Ошибка';
		return;
	}
	const { before, after } = calculated.value.numberOfCharactersBeforeAndAfter();
	if (after >= 20 || before > 10) {
		calculated.value = Number(calculated.value).toExponential(4);
	}
	if (after > 8 && after < 20) {
		calculated.value = Number(calculated.value).toFixed(8);
	}
};

const clickRemove = () => {
	input.value = '';
	calculated.value = '0';
	currentMode.value = '';
};
const clickCalculation = mode => {
	currentMode.value = mode;
	if (input.value != '') {
		calculated.value = input.value;
	}
	input.value = '';
};

const clickChangeOfSign = () => {
	if (input.value != '') {
		calculated.value = input.value;
	}
	input.value = '';
	if (Math.sign(Number(calculated.value)) == 1) {
		calculated.value = '-' + calculated.value;
	} else if (Math.sign(Number(calculated.value)) == -1) {
		calculated.value = Math.abs(Number(calculated.value));
	} else if (calculated.value == '-0') {
		calculated.value = '0';
	} else if (calculated.value == '0') {
		calculated.value = '-0';
	}
};
const clickPercent = () => {
	if (input.value != '') {
		calculated.value = input.value;
	}
	input.value = '';
	calculated.value = Number(calculated.value) / 100;
};
</script>

<template>
	<div class="flex justify-center h-screen items-center z-10 bg-slate-200">
		<div class="bg-black text-white rounded-2xl p-5 shadow-md">
			<div class="flex justify-end pt-10 mx-1">
				<p class="text-3xl">{{ input.length == 0 ? calculated : input }}</p>
			</div>
			<div class="grid grid-rows-5 gap-3 grid-flow-col mt-3">
				<button
					class="bg-neutral-300 p-2 rounded-full text-black w-10 h-10 active:bg-neutral-200 transition"
					@click="clickRemove"
				>
					{{ input.length == 0 && calculated == '0' ? 'AC' : 'C' }}
				</button>
				<button
					class="bg-neutral-600 p-2 rounded-full w-10 h-10 active:bg-neutral-400 transition"
					@click="clickNumber(7)"
				>
					7
				</button>
				<button
					class="bg-neutral-600 p-2 rounded-full w-10 h-10 active:bg-neutral-400 transition"
					@click="clickNumber(4)"
				>
					4
				</button>
				<button
					class="bg-neutral-600 p-2 rounded-full w-10 h-10 active:bg-neutral-400 transition"
					@click="clickNumber(1)"
				>
					1
				</button>
				<button
					class="col-span-2 bg-neutral-600 p-2 rounded-full active:bg-neutral-400 transition"
					@click="clickNumber(0)"
				>
					0
				</button>
				<button
					class="bg-neutral-300 p-2 rounded-full text-black w-10 h-10 active:bg-neutral-200 transition"
					@click="clickChangeOfSign"
				>
					+/-
				</button>
				<button
					class="bg-neutral-600 p-2 rounded-full w-10 h-10 active:bg-neutral-400 transition"
					@click="clickNumber(8)"
				>
					8
				</button>
				<button
					class="bg-neutral-600 p-2 rounded-full w-10 h-10 active:bg-neutral-400 transition"
					@click="clickNumber(5)"
				>
					5
				</button>
				<button
					class="bg-neutral-600 p-2 rounded-full w-10 h-10 active:bg-neutral-400 transition"
					@click="clickNumber(2)"
				>
					2
				</button>

				<button
					class="bg-neutral-300 p-2 rounded-full text-black w-10 h-10 active:bg-neutral-200 transition"
					@click="clickPercent"
				>
					%
				</button>
				<button
					class="bg-neutral-600 p-2 rounded-full w-10 h-10 active:bg-neutral-400 transition"
					@click="clickNumber(9)"
				>
					9
				</button>
				<button
					class="bg-neutral-600 p-2 rounded-full w-10 h-10 active:bg-neutral-400 transition"
					@click="clickNumber(6)"
				>
					6
				</button>
				<button
					class="bg-neutral-600 p-2 rounded-full w-10 h-10 active:bg-neutral-400 transition"
					@click="clickNumber(3)"
				>
					3
				</button>
				<button
					class="bg-neutral-600 p-2 rounded-full w-10 h-10 active:bg-neutral-400 transition"
					@click="clickNumber('.')"
				>
					.
				</button>
				<button
					:class="[
						'bg-orange-400',
						'p-2',
						'rounded-full',
						'w-10',
						'h-10',
						'active:bg-orange-300 ',
						'transition',
						{ 'bg-white': currentMode == 'division' },
						{ 'text-orange-400': currentMode == 'division' },
					]"
					@click="clickCalculation('division')"
				>
					&#247;
				</button>
				<button
					:class="[
						'bg-orange-400',
						'p-2',
						'rounded-full',
						'w-10',
						'h-10',
						'active:bg-orange-300 ',
						'transition',
						{ 'bg-white': currentMode == 'multiplication' },
						{ 'text-orange-400': currentMode == 'multiplication' },
					]"
					@click="clickCalculation('multiplication')"
				>
					&#215;
				</button>
				<button
					:class="[
						'bg-orange-400',
						'p-2',
						'rounded-full',
						'w-10',
						'h-10',
						'active:bg-orange-300 ',
						'transition',
						{ 'bg-white': currentMode == 'subtraction' },
						{ 'text-orange-400': currentMode == 'subtraction' },
					]"
					@click="clickCalculation('subtraction')"
				>
					-
				</button>
				<button
					:class="[
						'bg-orange-400',
						'p-2',
						'rounded-full',
						'w-10',
						'h-10',
						'active:bg-orange-300 ',
						'transition',
						{ 'bg-white': currentMode == 'addition' },
						{ 'text-orange-400': currentMode == 'addition' },
					]"
					@click="clickCalculation('addition')"
				>
					+
				</button>
				<button
					class="bg-orange-400 p-2 rounded-full w-10 h-10 active:bg-orange-300 transition"
					@click="clickEquals"
				>
					=
				</button>
			</div>
		</div>
	</div>
</template>

<style scoped></style>
