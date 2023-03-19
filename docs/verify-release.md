# Verifying the Knative Serving Package Release

This package is published as an OCI artifact, signed with Sigstore [Cosign](https://docs.sigstore.dev/cosign/overview), and associated with a [SLSA Provenance](https://slsa.dev/provenance) attestation.

Using `cosign`, you can display the supply chain security related artifacts for the `ghcr.io/kadras-io/cartographer-delivery` images. Use the specific digest you'd like to verify.

```shell
cosign tree ghcr.io/kadras-io/cartographer-delivery
```

The result:

```shell
📦 Supply Chain Security Related artifacts for an image: ghcr.io/kadras-io/cartographer-delivery
└── 💾 Attestations for an image tag: ghcr.io/kadras-io/cartographer-delivery:sha256-a76648eda1c2e807cf4a5d8b171343187a77034a678c338873a84300151bd697.att
   └── 🍒 sha256:2fe8282432e8f961a67ac7c8d8c1f1b5ac5063293afcd315be896dc49dd2bc56
└── 🔐 Signatures for an image tag: ghcr.io/kadras-io/cartographer-delivery:sha256-a76648eda1c2e807cf4a5d8b171343187a77034a678c338873a84300151bd697.sig
   └── 🍒 sha256:1ffe1a2b0b7d0d972ba8c73cb9e34ae144b641b25dcdc0028f3b500e845284ba
```

You can verify the signature and its claims:

```shell
cosign verify \
   --certificate-identity-regexp https://github.com/kadras-io \
   --certificate-oidc-issuer https://token.actions.githubusercontent.com \
   ghcr.io/kadras-io/cartographer-delivery | jq
```

You can also verify the SLSA Provenance attestation associated with the image.

```shell
cosign verify-attestation --type slsaprovenance \
   --certificate-identity-regexp https://github.com/slsa-framework \
   --certificate-oidc-issuer https://token.actions.githubusercontent.com \
   ghcr.io/kadras-io/cartographer-delivery | jq .payload -r | base64 --decode | jq
```
