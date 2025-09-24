<template>
    <div class="bg-white" id="demo">
        <div class="container py-5">
            <!-- Demo Header -->
            <div class="text-center mb-5">
                <h2 class="h3 fw-light text-black mb-3" style="letter-spacing: -0.5px;">Interactive Demo</h2>
                <p class="text-black-50 mb-0">Compare voice conversion models across source and target speakers</p>
            </div>

            <!-- Model Selection -->
            <div class="row justify-content-center mb-4">
                <div class="col-lg-10">
                    <div class="row">
                        <div class="col-md-6 mb-4">
                            <div class="border p-4">
                                <h4 class="h6 fw-bold text-uppercase mb-3 text-black" style="letter-spacing: 1px;">
                                    Model A</h4>
                                <select id="leftModel" class="form-select form-select-lg border-dark mb-3"
                                    v-model="leftModel">
                                    <option v-for="model in availableModels" :key="model" :value="model">{{ model }}
                                    </option>
                                </select>
                                <div class="d-flex justify-content-between text-black-50">
                                    <div class="text-center">
                                        <div class="fw-bold text-black">{{ modelInfos[leftModel].latency }}</div>
                                        <small>Latency</small>
                                    </div>
                                    <div class="text-center">
                                        <div class="fw-bold text-black">{{ modelInfos[leftModel].rtf }}</div>
                                        <small>RTF</small>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="col-md-6 mb-4">
                            <div class="border p-4">
                                <h4 class="h6 fw-bold text-uppercase mb-3 text-black" style="letter-spacing: 1px;">
                                    Model B</h4>
                                <select id="rightModel" class="form-select form-select-lg border-dark mb-3"
                                    v-model="rightModel">
                                    <option v-for="model in availableModels" :key="model" :value="model">{{ model }}
                                    </option>
                                </select>
                                <div class="d-flex justify-content-between text-black-50">
                                    <div class="text-center">
                                        <div class="fw-bold text-black">{{ modelInfos[rightModel].latency }}</div>
                                        <small>Latency</small>
                                    </div>
                                    <div class="text-center">
                                        <div class="fw-bold text-black">{{ modelInfos[rightModel].rtf }}</div>
                                        <small>RTF</small>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Target Utterance Selection -->
            <div class="row justify-content-center mb-5">
                <div class="col-lg-6">
                    <div class="target-card bg-light p-3 rounded border">
                        <h4 class="h6 fw-bold text-uppercase mb-3 text-black text-center" style="letter-spacing: 1px;">
                            Target Utterance</h4>
                        <select id="targetUtterance" class="form-select form-select-lg border-dark mb-3"
                            v-model="selectedTarget">
                            <option v-for="(target, idx) in demoTargets" :key="idx" :value="idx">{{ target.name }}
                            </option>
                        </select>
                        <div v-if="selectedTarget !== null" class="text-center">
                            <audio :src="demoTargets[selectedTarget].url" controls class="w-100"></audio>
                        </div>
                    </div>
                </div>
            </div>


            <!-- Comparison Results for Selected Target - Two Tables Side by Side -->
            <div v-if="selectedTarget !== null" class="row">
                <!-- First Table - First Half of Sources -->
                <div class="col-lg-6">
                    <div class="table-responsive">
                        <table class="table table-bordered align-middle border-dark">
                            <thead class="border-dark">
                                <tr>
                                    <th class="bg-black text-white text-center py-3" style="width: 180px;">
                                        <div class="fw-bold">Source Utterance</div>
                                    </th>
                                    <th class="bg-black text-white text-center border-dark py-3" style="width: 200px;">
                                        <div class="fw-bold">{{ leftModel }}</div>
                                    </th>
                                    <th class="bg-black text-white text-center border-dark py-3" style="width: 200px;">
                                        <div class="fw-bold">{{ rightModel }}</div>
                                    </th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="(source, sIdx) in firstHalfSources" :key="sIdx" class="border-dark">
                                    <!-- Source Audio Header -->
                                    <td class="bg-black text-white text-center py-3 border-dark">
                                        <div class="fw-bold mb-2 small">{{ source.name }}</div>
                                        <audio :src="source.url" controls class="w-100" style="height: 32px;"></audio>
                                    </td>

                                    <!-- Model A Result -->
                                    <td class="p-3 border-dark text-center">
                                        <div
                                            v-if="demoResults[selectedTarget] && demoResults[selectedTarget][source.originalIndex]">
                                            <audio v-if="demoResults[selectedTarget][source.originalIndex].left"
                                                :src="demoResults[selectedTarget][source.originalIndex].left" controls
                                                class="w-100" style="height: 32px;"></audio>
                                        </div>
                                        <div v-else class="py-2">
                                            <div class="text-muted small">Loading...</div>
                                        </div>
                                    </td>

                                    <!-- Model B Result -->
                                    <td class="p-3 border-dark text-center">
                                        <div
                                            v-if="demoResults[selectedTarget] && demoResults[selectedTarget][source.originalIndex]">
                                            <audio v-if="demoResults[selectedTarget][source.originalIndex].right"
                                                :src="demoResults[selectedTarget][source.originalIndex].right" controls
                                                class="w-100" style="height: 32px;"></audio>
                                        </div>
                                        <div v-else class="py-2">
                                            <div class="text-muted small">Loading...</div>
                                        </div>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>

                <!-- Second Table - Second Half of Sources -->
                <div class="col-lg-6">
                    <div class="table-responsive">
                        <table class="table table-bordered align-middle border-dark">
                            <thead class="border-dark">
                                <tr>
                                    <th class="bg-black text-white text-center py-3" style="width: 180px;">
                                        <div class="fw-bold">Source Utterance</div>
                                    </th>
                                    <th class="bg-black text-white text-center border-dark py-3" style="width: 200px;">
                                        <div class="fw-bold">{{ leftModel }}</div>
                                    </th>
                                    <th class="bg-black text-white text-center border-dark py-3" style="width: 200px;">
                                        <div class="fw-bold">{{ rightModel }}</div>
                                    </th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="(source, sIdx) in secondHalfSources" :key="sIdx" class="border-dark">
                                    <!-- Source Audio Header -->
                                    <td class="bg-black text-white text-center py-3 border-dark">
                                        <div class="fw-bold mb-2 small">{{ source.name }}</div>
                                        <audio :src="source.url" controls class="w-100" style="height: 32px;"></audio>
                                    </td>

                                    <!-- Model A Result -->
                                    <td class="p-3 border-dark text-center">
                                        <div
                                            v-if="demoResults[selectedTarget] && demoResults[selectedTarget][source.originalIndex]">
                                            <audio v-if="demoResults[selectedTarget][source.originalIndex].left"
                                                :src="demoResults[selectedTarget][source.originalIndex].left" controls
                                                class="w-100" style="height: 32px;"></audio>
                                        </div>
                                        <div v-else class="py-2">
                                            <div class="text-muted small">Loading...</div>
                                        </div>
                                    </td>

                                    <!-- Model B Result -->
                                    <td class="p-3 border-dark text-center">
                                        <div
                                            v-if="demoResults[selectedTarget] && demoResults[selectedTarget][source.originalIndex]">
                                            <audio v-if="demoResults[selectedTarget][source.originalIndex].right"
                                                :src="demoResults[selectedTarget][source.originalIndex].right" controls
                                                class="w-100" style="height: 32px;"></audio>
                                        </div>
                                        <div v-else class="py-2">
                                            <div class="text-muted small">Loading...</div>
                                        </div>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, watch, onMounted, computed } from 'vue'

// Helper function to get the correct asset URL for GitHub Pages
const getAssetUrl = (path) => {
    const base = import.meta.env.BASE_URL || '/'
    // Remove leading slash from path and ensure base ends with slash
    const cleanPath = path.startsWith('/') ? path.slice(1) : path
    const cleanBase = base.endsWith('/') ? base : base + '/'
    return cleanBase + cleanPath
}

const availableModels = [
    'DarkStream-v2-logits',
    'DarkStream-bottleneck-tvtimbre',
    'DarkStream-bottleneckvq-tvtimbre',
    'GenVC-small',
    'GenVC-large',
    'GenVC-small-nonstreaming',
    'GenVC-large-nonstreaming'
]
const modelInfos = {
    'DarkStream-v2-logits': {
        latency: 'TODO',
        rtf: 'TODO',
    },
    'DarkStream-bottleneck-tvtimbre': {
        latency: 'TODO',
        rtf: 'TODO',
    },
    'DarkStream-bottleneckvq-tvtimbre': {
        latency: 'TODO',
        rtf: 'TODO',
    },
    'GenVC-small': {
        latency: '0.29 ± 0.03',
        rtf: '0.83 ± 0.05',
    },
    'GenVC-large': {
        latency: '0.32 ± 0.11',
        rtf: '0.83 ± 0.07',
    },
    'GenVC-small-nonstreaming': {
        latency: 'None',
        rtf: 'None',
    },
    'GenVC-large-nonstreaming': {
        latency: 'None',
        rtf: 'None',
    },
}

const leftModel = ref('DarkStream-v2-logits')
const rightModel = ref('GenVC-small')

// Target selection
const selectedTarget = ref(0) // Default to first target

// Demo mode data
const demoResults = ref({}) // Store results for each target-source combination
const isProcessing = ref(false)

// Demo audio files - organized structure
const demoSources = ref([
    { name: 'RRBI_arctic_a0055', url: getAssetUrl('/audio/demo/sources/RRBI_arctic_a0055.wav') },
    { name: 'TNI_arctic_a0356', url: getAssetUrl('/audio/demo/sources/TNI_arctic_a0356.wav') },
    { name: 'BDL_arctic_a0406', url: getAssetUrl('/audio/demo/sources/BDL_arctic_a0406.wav') },
    { name: 'MBMPS_arctic_b0244', url: getAssetUrl('/audio/demo/sources/MBMPS_arctic_b0244.wav') },
    { name: 'CLB_arctic_b0322', url: getAssetUrl('/audio/demo/sources/CLB_arctic_b0322.wav') }
])

const demoTargets = ref([
    { name: 'ERMS_arctic_a0113', url: getAssetUrl('/audio/demo/targets/ERMS_arctic_a0113.wav') },
    { name: 'ASI_arctic_a0292', url: getAssetUrl('/audio/demo/targets/ASI_arctic_a0292.wav') },
    { name: 'SLT_arctic_a0334', url: getAssetUrl('/audio/demo/targets/SLT_arctic_a0334.wav') },
    { name: 'SVBI_arctic_a0508', url: getAssetUrl('/audio/demo/targets/SVBI_arctic_a0508.wav') },
    { name: 'NJS_arctic_b0170', url: getAssetUrl('/audio/demo/targets/NJS_arctic_b0170.wav') }
])

// Computed properties to split sources into two halves
const firstHalfSources = computed(() => {
    const midPoint = Math.ceil(demoSources.value.length / 2)
    return demoSources.value.slice(0, midPoint).map((source, index) => ({
        ...source,
        originalIndex: index
    }))
})

const secondHalfSources = computed(() => {
    const midPoint = Math.ceil(demoSources.value.length / 2)
    return demoSources.value.slice(midPoint).map((source, index) => ({
        ...source,
        originalIndex: index + midPoint
    }))
})

// Generate results only for the selected target
const generateSelectedTargetResults = async () => {
    if (selectedTarget.value === null) return

    isProcessing.value = true
    try {
        const targetIdx = selectedTarget.value

        if (!demoResults.value[targetIdx]) {
            demoResults.value[targetIdx] = {}
        }

        for (let sIdx = 0; sIdx < demoSources.value.length; sIdx++) {
            const sourceName = demoSources.value[sIdx].url.split('/').pop().replace('.wav', '')
            const targetName = demoTargets.value[targetIdx].url.split('/').pop().replace('.wav', '')

            demoResults.value[targetIdx][sIdx] = {
                left: getAssetUrl(`/audio/demo/results/${leftModel.value}/${targetName}/${sourceName}.wav`),
                right: getAssetUrl(`/audio/demo/results/${rightModel.value}/${targetName}/${sourceName}.wav`)
            }
        }

        // Force reactivity update
        demoResults.value = { ...demoResults.value }

    } catch (error) {
        console.error('Voice conversion failed:', error)
    } finally {
        isProcessing.value = false
    }
}

// Generate all demo combinations at once
const generateAllDemoVC = async () => {
    isProcessing.value = true
    try {
        const newResults = {}

        // Generate results for all combinations
        for (let tIdx = 0; tIdx < demoTargets.value.length; tIdx++) {
            newResults[tIdx] = {}
            for (let sIdx = 0; sIdx < demoSources.value.length; sIdx++) {
                // Get basenames for more readable filenames
                const sourceName = demoSources.value[sIdx].url.split('/').pop().replace('.wav', '')
                const targetName = demoTargets.value[tIdx].url.split('/').pop().replace('.wav', '')
                newResults[tIdx][sIdx] = {
                    left: getAssetUrl(`/audio/demo/results/${leftModel.value}/${targetName}/${sourceName}.wav`),
                    right: getAssetUrl(`/audio/demo/results/${rightModel.value}/${targetName}/${sourceName}.wav`)
                }
            }
        }

        demoResults.value = newResults

    } catch (error) {
        console.error('Batch voice conversion failed:', error)
    } finally {
        isProcessing.value = false
    }
}

// Watch for model changes and target selection to regenerate demo results
watch([leftModel, rightModel, selectedTarget], () => {
    if (selectedTarget.value !== null) {
        generateSelectedTargetResults()
    }
})

// Generate demo results for selected target when component mounts
onMounted(() => {
    generateSelectedTargetResults()
})
</script>

<style scoped>
.table {
    max-width: 700px;
    margin: 0 auto;
}

.table th,
.table td {
    padding: 0.75rem;
}

/* Target Card Styling */
.target-card {
    background-color: #f8f9fa !important;
    border: 2px solid #dee2e6 !important;
}

/* Clean, simple styling */
</style>