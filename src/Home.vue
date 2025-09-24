<template>
    <div class="min-vh-100 bg-white">
        <!-- Paper Header -->
        <div class="border-bottom">
            <div class="container py-5">
                <div class="text-center">
                    <!-- Paper Title -->
                    <h1 class="display-5 fw-light text-black mb-4 lh-base"
                        style="font-weight: 300; letter-spacing: -1px;">
                        Content-Synchronous Time-Varying Timbre<br>
                        <span class="fw-bold">for Streaming Voice Anonymization</span>
                    </h1>

                    <!-- Conference Info -->
                    <div class="mb-5">
                        <p class="text-black fs-6 mb-0">Anonymized submission for ICLR 2026</p>
                    </div>

                    <!-- Abstract -->
                    <div class="row justify-content-center">
                        <div class="col-lg-9">
                            <div class="text-start">
                                <h2 class="h6 fw-bold text-uppercase text-black mb-3 tracking-wide"
                                    style="letter-spacing: 2px;">Abstract</h2>
                                <p class="text-black lh-lg mb-0" style="font-size: 1.1rem; font-weight: 400;">
                                    Real-time voice conversion and speech anonymization require causal, low-latency
                                    synthesis without sacrificing intelligibility or naturalness. A core limitation of
                                    current systems is a representational mismatch: content is time-varying, while
                                    speaker
                                    identity is injected as a static global embedding, yielding over-smoothed timbre and
                                    reduced expressivity. We introduce a streamable speech synthesizer that aligns the
                                    temporal granularity of identity and content via a <strong>content-synchronous,
                                        time-varying
                                        timbre representation</strong>. A Global Timbre Memory expands a global timbre
                                    seed into compact
                                    facets; frame-level content attends to this memory, a gate regulates variation, and
                                    spherical interpolation preserves identity geometry while enabling smooth local
                                    changes.
                                    Complementing this, a factorized vector-quantized bottleneck regularizes content to
                                    reduce residual speaker leakage. The resulting system is fully streamable, with
                                    <strong>&lt;80 ms
                                        GPU latency</strong>. Experiments demonstrate improvements in naturalness,
                                    speaker
                                    similarity, and anonymization strength compared to state-of-the-art streaming
                                    baselines, establishing TVT as a scalable approach for privacy-preserving and
                                    expressive speech synthesis under strict latency budgets.
                                </p>
                            </div>
                        </div>
                    </div>

                    <!-- Links -->
                    <div class="mt-5">
                        <a href="#" class="btn btn-outline-dark me-3 px-4 py-2">Paper</a>
                        <!-- <a href="#" class="btn btn-dark me-3 px-4 py-2">Code</a> -->
                        <a href="#demo" class="btn btn-outline-dark px-4 py-2">Demo ↓</a>
                    </div>
                </div>
            </div>
        </div>

        <!-- Architecture Section -->
        <div class="bg-light border-top border-bottom">
            <div class="container py-5">
                <div class="text-center mb-4">
                    <h2 class="h4 fw-light text-black mb-3" style="letter-spacing: -0.5px;">System Architecture</h2>
                    <p class="text-black-50 mb-4">Overview of the Content-Synchronous Time-Varying Timbre framework</p>
                </div>
                <div class="row justify-content-center">
                    <div class="col-lg-10">
                        <div class="text-center">
                            <img :src="getAssetUrl('/architectures/architecture.png')" alt="System Architecture Diagram"
                                class="img-fluid rounded shadow-sm"
                                style="max-width: 100%; height: auto; border: 1px solid #dee2e6;">
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Voice Comparison Demo Component -->
        <VoiceComparisonDemo />
    </div>
</template>

<script setup>
import VoiceComparisonDemo from '@/components/VoiceComparisonDemo.vue'

// Helper function to get the correct asset URL for GitHub Pages
const getAssetUrl = (path) => {
    const base = import.meta.env.BASE_URL || '/'
    // Remove leading slash from path and ensure base ends with slash
    const cleanPath = path.startsWith('/') ? path.slice(1) : path
    const cleanBase = base.endsWith('/') ? base : base + '/'
    return cleanBase + cleanPath
}
</script>

<style scoped>
/* Bootstrap handles styling */
</style>
