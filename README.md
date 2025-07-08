# Smart Contracts for the Digital Identity Ecosystem

These smart contracts present a digital identity ecosystem built upon a hierarchical structure of Blockchain tokens, where both government-certified and uncertified tokens can coexist to represent various attributes of an individual's identity. These smart contracts are composed of two interfaces *OwnerInterface* and *UsersInterface* and two smart contracts (*Users* and *DigitalIdentity*) that inherit from the interfaces.
The interfaces work as standards that must be implemented if new tokens want to be created. Here, we illustrate two examples, one certified by a government called *BirthCertificate* and the other not certified by the government but by a university called *ScholarCurriculum*.

## UsersInterface Overview
This is a Solidity interface contract, specifically designed for Web3 development. It defines an API that other contracts can use to interact with users.

Here's what each function does:

**getType(address)**: Returns the type of user associated with a given address.

**getCreator(address _address)**: Returns the creator (owner) of a digital identity, identified by its owner's address.

**getDigIdentityAdd(address _address)**: Returns an additional address related to a specific digital identity.

**userExists(address _address)**: Checks if a user with the given address exists.
This interface allows other contracts to call these functions without having access to their implementation, promoting modularity and reusability in smart contract development.

## OwnerInterface Overview
This code defines a Solidity interface called *OwnerInterface*. It has three functions:

**owner()**: Returns the address of the owner.

**nameToken()**: Returns the name token as a string.

**government()**: Returns the government contract's address.

These functions are declared as "view" which means they don't modify state and can be called without spending gas, making them suitable for reading-only operations.

## Users Contract Overview

The Users contract is a Solidity Contract that manages digital identities for users. It allows the government to create new user accounts, and administers their types. 

**Attributes**:
- **User Struct**: Represents an individual user with attributes:
- **creator**: Address of the entity creating the user.
- **didentity**: Address of the associated Digital Identity contract (see *DigitalIdentity.sol*).
- **userType**: Integer representing the type of user (0 = Government, 1 = Admin, others = regular users).
- **Mapping**: Stores all created user accounts with their respective addresses as keys.

**Modifiers**:
- **onlyGovernmentOrAdmin(int _userType)**: Ensures only government or admin can execute specific actions based on the provided _userType.
- **userExistsIn(address _address)**: Validates that a user exists for any given address.

**Functions**:
- **registerUser**: Allows government to create new users with associated Digital Identity contracts.
- **getType**: Returns the user's type given their address.
- **getCreator**: Returns the creator of the user (i.e., who registered them).
- **userExists**: Checks if a user exists for a given address.
- **getDigIdentityAdd**: Returns the address of the digital identity associated with a user.

## DigitalIdentity Contract Overview
This contract, *DigitalIdentity*, is a Solidity implementation that manages digital identities and their associated tokens. It imports interfaces from *OwnerInterface* and *UsersInterface* to interact with them.

**Error Handling**: The contract defines five error codes for incorrect government, owner, or token existence.

**Attributes**: 
- Stores the *owner*'s address
- Has a *nameToken* attribute set to *DigitalIdentity*
- Tracks linked tokens and their creators

**Linked Tokens Struct**: A struct representing each linked token with attributes *tokenAdd*, *nameToken*, *creator*, *gcert* (government certification), and *exists*.

**Constructor**:
- Validates the sender's address as a user contract
- Checks if the provided government is valid using the users interface

**Functions**:
- **linkToken**: Links an existing token to this digital identity, requiring owner approval.
- **numberOfLinkedTokens**, **getNameToken**, **creatorIsGovernment**, and **getCreatorOfToken** are view functions that provide information about linked tokens.

## BirthCertificate Contract Overview
This is a Solidity smart contract, which stores information about birth certificates.

**Attributes**:

- *name*, *fLastName*, *mLastName*: attributes to form the full name.
- *gender*: true for male, false for female
- *day, month, year*: date of birth
- *state, municipalty*: place of birth
- *Events*: The contract emits an event called governmentTransactions when certain actions are performed.

**Functions**:

- *Constructor*: Initializes the contract with user-provided data and checks that the creator is a valid government user.
- *setFatherAddress* and *setMotherAddress*: Allow setting father's/mother's addresses, which can only be done by authorized government users (using modifiers).
- *getDay, getMonth, getYear*, etc.: Return specific attributes of the birth certificate.

**Modifiers**:

- *mustBeGovernment*: Ensures that a function is executed by an authorized government user.
- *ownerOrGovernment*: Allows execution by either the contract's owner or an authorized government user.

## ScholarCurriculum Contract Overview
This contract is designed for storing and managing student curriculum data.

**Key Features**:
- Student Attributes: The contract has several public variables to store attributes like *studentId*, *name*, *fLastName*, *mLastName*, and others.
- Achievements Structure: It defines an inner struct called Achievement with fields for *ID*, *title*, *date*, and *details* of the achievement.
- Mapping Achievements: The contract uses a mapping to store achievements by their IDs (*uint* => *Achievement*) and keeps track of the current id using *idAch*.

**Security Features**:
It checks if the message sender is indeed the university (using modifier mustBeUniversity).

**Functions**:
- *addAchievement*: adds a new achievement to the mapping, incrementing the ID counter.
- *numberOfAchievements*: returns the total number of achievements stored in the contract.
- *getAchievement*: retrieves an achievement by its ID as JSON-formatted string.
