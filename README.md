# HyperVisorBridge
An in-memory AMD SVM (Ring-1) execution bridge launched via a Windows kernel driver. The hypervisor core is intentionally minimal and frozen; all logic is driven externally through a shared VMCB mailbox.
