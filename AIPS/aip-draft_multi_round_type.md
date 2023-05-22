---
aip:
title: Deploy multiple types of round from the same factory
status: Draft
type: Core
author: BootNode, Lisandro Corbalan <lisandro@bootnode.dev>
created: 2023-05-22
---

## Abstract

This AIP propose a changes to the `IRoundFactory`, `RoundFactory` and `IRoundImplementation` to provide a way of deploying multiple types of rounds from the same factory contract.

## Motivation

The current implementation of the round factory only allows the creation of a single type of round, the one that is set as the round implementation, and the round interface is coupled to the current round implementation.

As a result, we want to improve the current round deployment process to allow new types of round to be deployed from the same factory contract, and to have a common interface to be used for defining new type of rounds.

## Goals

- Create a registry of allowed round implementation in the `RoundFactory` contract were each implementation will be identified by a type.
- Deploy multiple types of rounds from a single factory contract, based on a given type identifier.
- Make the `IRoundImplementation` a more generic interface.

## Specification

`RoundFactory`
- set new version
- add `mapping(bytes32 => address) roundTypes` ( type id -> round implementation address)
- add `function updateRoundType(bytes roundType, address newRoundImplementation) external onlyOwner`
- remove `updateRoundImplementation` function
- update `create` function
    - add `bytes32 roundType` parameter
    - deploy round based on `roundType`
- update `event RoundCreated`
    - add `bytes32 roundType`
- remove `event RoundImplementationUpdated`
- add `event RoundTypeUpdated(bytes32 roundType, address roundImplementation)`

`IRoundFactory`
- update definition based on the changes made to `RoundFactory`

`IRoundImplementation`
- remove `vote` function
- remove `setReadyForPayout` function
- remove `updateStartAndEndTimes`
- update `applyToRound`
  - add `bytes encodedParameters` which would be decoded on each round type implementation
