<script lang="ts" setup>
const input = ref("");
function transformInput(input: string) {
  const lines = input.split("\n");
  const output: string[] = [];
  let mode = "SORTIE";
  console.log(lines)
  let currentCategory = "";
  let currentTotal = 0;
  let globalTotal = 0;
  let currentEntryTotal = 0;
  lines.forEach((line) => {
    console.log(line)
    if (line.length > 2) {
      if (line == "ENTREE\t") {
        mode = "ENTREE";
      }
      if (mode == "SORTIE" && line !== "SORTIE\t") {
        const [category, amountStr] = line.split("\t");
        let amount = parseFloat(amountStr.replace(",", "."));

        if (category.startsWith("*")) {
          output.push(`${currentCategory} [${Math.round(amount)}] ${category.substring(1)}`);
          amount = 0;
        } else {
          currentCategory = category;
          currentTotal = 0;
          output.push(`Budget [${Math.round(amount)}] ${category}`);
        }

        currentTotal += amount;
        globalTotal += amount;
      } else if (line != "ENTREE\t" && line != "SORTIE\t") {
        const [category, amountStr] = line.split("\t");
        let amount = parseFloat(amountStr.replace(",", "."));
        output.push(`${category} [${Math.round(amount)}] Budget`);
        currentEntryTotal += amount;
      }
    }
  });

  if (currentEntryTotal < globalTotal && currentEntryTotal!==0) {
    output.push(`Epargne [${Math.round(globalTotal - currentEntryTotal)}] Budget`);
  }
  return output.join("\n");
}
</script>
<template>
  <div class="bg-gray-800 h-screen flex justify-center items-center">
    <div class="w-full">
      <h1 class="text-white font-bold text-3xl lg:text-6xl font-mono text-center mb-12">ExcelToSankey</h1>
      <div class="flex gap-12 md:w-[80%] m-auto">
        <div class="bg-white p-4 rounded-md shadow-lg shadow-white/30 w-full aspect-square">
          <label for="input" class="block text-sm font-medium text-gray-700">Input</label>
          <textarea v-model="input" id="input" class="mt-1 p-2 border w-full h-full rounded-md resize-none"></textarea>
        </div>
        <div class="bg-white p-4 rounded-md shadow-lg shadow-white/30 w-full aspect-square">
          <label for="output" class="block text-sm font-medium text-gray-700">Output</label>
          <textarea id="output" class="mt-1 p-2 border w-full h-full rounded-md resize-none" readonly>{{
            transformInput(input)
          }}</textarea>
        </div>
      </div>
    </div>
  </div>
</template>
