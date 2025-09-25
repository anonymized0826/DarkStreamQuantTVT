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
                                        <div class="fw-bold text-black">{{ modelInfos[leftModel].nisqa_mos }}</div>
                                        <small>NISQA-MOS</small>
                                    </div>
                                    <div class="text-center">
                                        <div class="fw-bold text-black">{{ modelInfos[leftModel].wer }}</div>
                                        <small>WER</small>
                                    </div>
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
                                        <div class="fw-bold text-black">{{ modelInfos[rightModel].nisqa_mos }}</div>
                                        <small>NISQA-MOS</small>
                                    </div>
                                    <div class="text-center">
                                        <div class="fw-bold text-black">{{ modelInfos[rightModel].wer }}</div>
                                        <small>WER</small>
                                    </div>
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
                        <div v-if="selectedTarget !== null && demoTargets[selectedTarget]" class="text-center">
                            <audio :src="demoTargets[selectedTarget].url" controls class="w-100"></audio>
                        </div>
                    </div>
                </div>
            </div>


            <!-- Loading State -->
            <div v-if="isLoading" class="text-center py-5">
                <div class="spinner-border text-dark" role="status">
                    <span class="visually-hidden">Loading audio files...</span>
                </div>
                <p class="mt-3 text-muted">Loading audio files...</p>
            </div>

            <!-- Comparison Results for Selected Target - Two Tables Side by Side -->
            <div v-else-if="selectedTarget !== null && demoSources.length > 0 && demoTargets.length > 0" class="row">
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
    'slt24',
    'DarkStream',
    'TVTSyn-novq',
    'TVTSyn',
    'GenVC-small',
]
const modelInfos = {
    'slt24': {
        nisqa_mos: '4.02',
        wer: '5.70%',
        latency: '86.49',
        rtf: '0.44',
    },
    'DarkStream': {
        nisqa_mos: '2.66',
        wer: '10.80%',
        latency: '79.76',
        rtf: '0.33',
    },
    'TVTSyn-novq': {
        nisqa_mos: '3.45',
        wer: '-',
        latency: '-',
        rtf: '-',
    },
    'TVTSyn': {
        nisqa_mos: '3.91',
        wer: '5.35%',
        latency: '78.03',
        rtf: '0.30',
    },
    'GenVC-small': {
        nisqa_mos: '3.60',
        wer: '8.20%',
        latency: '-',
        rtf: '-',
    },
}

const leftModel = ref('TVTSyn')
const rightModel = ref('GenVC-small')

// Target selection
const selectedTarget = ref(0) // Default to first target

// Demo mode data
const demoResults = ref({}) // Store results for each target-source combination
const isProcessing = ref(false)
const isLoading = ref(true) // Loading state for audio files

// Demo audio files - automatically loaded
const demoSources = ref([])
const demoTargets = ref([])
const demoDir = ref('audio/demo2')

// Function to dynamically discover audio files from a directory
const loadAudioFiles = async (directory) => {
    const files = []

    try {
        // Use import.meta.glob to dynamically import all .wav files from the directory
        const audioModules = import.meta.glob('/public/audio/demo2/**/*.wav', {
            eager: false,
            query: '?url',
            import: 'default'
        })

        // Filter files based on the specific directory
        const directoryPattern = `/public/${demoDir.value}/${directory}/`

        for (const [path] of Object.entries(audioModules)) {
            if (path.startsWith(directoryPattern)) {
                const fileName = path.split('/').pop()
                const name = fileName.replace('.wav', '')
                const url = getAssetUrl(`/${demoDir.value}/${directory}/${fileName}`)

                files.push({ name, url })
            }
        }

        // Sort files alphabetically by name
        files.sort((a, b) => a.name.localeCompare(b.name))

    } catch (error) {
        console.error(`Error loading files from ${directory}:`, error)
    }

    return files
}

// Load audio files on component initialization
const initializeAudioFiles = async () => {
    isLoading.value = true
    try {
        const [sources, targets] = await Promise.all([
            loadAudioFiles('sources'),
            loadAudioFiles('targets')
        ])

        demoSources.value = sources
        demoTargets.value = targets

    } catch (error) {
        console.error('Error loading audio files:', error)
        // Fallback to hardcoded files if auto-loading fails
        demoSources.value = [
            { name: 'RRBI_arctic_a0055', url: getAssetUrl('/audio/demo/sources/RRBI_arctic_a0055.wav') },
            { name: 'TNI_arctic_a0356', url: getAssetUrl('/audio/demo/sources/TNI_arctic_a0356.wav') },
            { name: 'BDL_arctic_a0406', url: getAssetUrl('/audio/demo/sources/BDL_arctic_a0406.wav') },
            { name: 'MBMPS_arctic_b0244', url: getAssetUrl('/audio/demo/sources/MBMPS_arctic_b0244.wav') },
            { name: 'CLB_arctic_b0322', url: getAssetUrl('/audio/demo/sources/CLB_arctic_b0322.wav') }
        ]
        demoTargets.value = [
            { name: 'ERMS_arctic_a0113', url: getAssetUrl('/audio/demo/targets/ERMS_arctic_a0113.wav') },
            { name: 'ASI_arctic_a0292', url: getAssetUrl('/audio/demo/targets/ASI_arctic_a0292.wav') },
            { name: 'SLT_arctic_a0334', url: getAssetUrl('/audio/demo/targets/SLT_arctic_a0334.wav') },
            { name: 'SVBI_arctic_a0508', url: getAssetUrl('/audio/demo/targets/SVBI_arctic_a0508.wav') },
            { name: 'NJS_arctic_b0170', url: getAssetUrl('/audio/demo/targets/NJS_arctic_b0170.wav') }
        ]
    } finally {
        isLoading.value = false
    }
}

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
                left: getAssetUrl(`${demoDir.value}/results/${leftModel.value}/${targetName}/${sourceName}.wav`),
                right: getAssetUrl(`${demoDir.value}/results/${rightModel.value}/${targetName}/${sourceName}.wav`)
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


// Watch for model changes and target selection to regenerate demo results
watch([leftModel, rightModel, selectedTarget], () => {
    if (selectedTarget.value !== null) {
        generateSelectedTargetResults()
    }
})

// Initialize component when mounted
onMounted(async () => {
    await initializeAudioFiles()
    // Generate results after files are loaded
    if (demoSources.value.length > 0 && demoTargets.value.length > 0) {
        generateSelectedTargetResults()
    }
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