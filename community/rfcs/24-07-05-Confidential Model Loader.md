# Author
@kenplusplus, @dongx1x, @hdxia, @mkbhanda, @ruomengh, @jodh-intel,
@likebreath


# Status
Under Review

# Objective

This RFC presents the design of "Confidential Model Loader" for
ensuring the integrity and confidentiality of GenAI micro-services with
minimum code changes. It is designed to prevent not only model theft and
poisoning during development-time and runtime, but also disclosure of
the sensitive enterprise data fused in fine-tuned models.

# Motivation

To protect its integrity and confidentiality, it is required to cover
both the storage and usage of a GenAI enterprise model throughout its
entire lifecycle, from the training phase to the inference phase. This
also involves various confidential computing technologies to work
together seamlessly, such as vendor-specific trusted execution
environment (e.g. Intel TDX, AMD SEV/SEV-SNP, etc.), password-less full
disk encryption (FDE), and remote attestation. These technologies are
very complex and require domain expertise to use. Therefore, the
"Confidential Model Loader" is proposed to remove the adoption barrier
of using these confidential computing technologies for GenAI developers
and data scientists, so that GenAI models can be protected with minimum
code changes to their existing micro-services.

# Design Proposal

According to the threat model constructed by OWASP
(https://owaspai.org/docs/ai_security_overview/), GenAI models (both
foundation and fine-tune models) need integrity protection for model
poisoning and confidentiality protection for model theft. Also, the
protection needs to cover both model-in-store and model-in-use.

The overall design is as follows:

![](./confidential_model_loader_architecture.png)

The workflows is as follow:

- Phase of model-in-store:

  Models can be protected against model theft in a trusted execution
  environment (TEE) via the following steps (may be enhanced by
  confidential TEE or TPM):

  1. Create a model key with a key transfer policy, and register the
     model key to a Key Broker Server/Key Management Server
  2. Encrypt the model using the model key
  3. Publish the encrypted model into a model registry

- Phase of model-in-use:

  GenAI micro-services need run within a TEE. The "Confidential Model
  Loader" is designed as a "initContainer" to decrypt encrypted models
  via the following steps:

  1. Calculate the evidence of both launch time and runtime measurement
     at Node/Container level from configFS or vTPM
  2. Get model key via attestation services based on the collected
     evidences
  3. Decrypt the model and put it into a RAM disk or a data disk with
     FDE enabled

With this design, existing GenAI micro-services require minimum code
changes, which reduces the complexity of protecting GenAI models with
confidential computing technologies.

# Alternatives Considered
n/a

# Compatibility
n/a

# Miscs
