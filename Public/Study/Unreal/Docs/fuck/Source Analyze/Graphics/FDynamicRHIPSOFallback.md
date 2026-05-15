###### Inherited By
- FNullDynamicRHI 
- ID3D11DynamicRHI 
- IOpenGLDynamicRHI

###### Implements [FDynamicRHI](Public/Study/Unreal/Docs/fuck/Source%20Analyze/Graphics/FDynamicRHI.md)

```cpp
virtual FGraphicsPipelineStateRHIRef RHICreateGraphicsPipelineState(const FGraphicsPipelineStateInitializer& Initializer) override
{
	return new FRHIGraphicsPipelineStateFallBack(Initializer);
}

virtual FComputePipelineStateRHIRef RHICreateComputePipelineState(const FComputePipelineStateInitializer& Initializer) override
{
	return new FRHIComputePipelineStateFallback(Initializer.ComputeShader);
}

```



