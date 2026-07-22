# Development Log 003 - Ubuntu Server Migration

## Objective

Prepare and migrate Project Alpha from Windows to Ubuntu Server, establishing the foundation for a dedicated Linux infrastructure platform.

## Background

With hardware preparation and data preservation complete, the next phase focused on replacing the existing Windows installation with Ubuntu Server. This marked the transition from project planning into infrastructure deployment.

## Preparation

Prior to installation, the following tasks were completed:

- Verified successful backup of approximately 30 GB of personal data.
- Archived a hardware baseline using a DirectX Diagnostic (DxDiag) report.
- Downloaded Ubuntu Server 26.04 LTS.
- Created a bootable USB installation drive using Rufus.
- Configured the installation media for:
  - GPT Partition Scheme
  - UEFI (Non-CSM)
  - FAT32

## Deployment

The system was prepared for operating system migration following verification of the installation media and hardware documentation.

This milestone officially transitioned Project Alpha from a planning exercise into an actively deployed Linux infrastructure environment.

## Validation

The installation media was successfully created and verified.

During validation, Windows reported that portions of the USB drive contained an unrecognized file system. Investigation confirmed this behavior was expected because Linux boot partitions created by Rufus are not fully recognized by Windows.

No corrective action was required.

## Outcome

Project Alpha completed all pre-deployment activities and was ready for Ubuntu Server installation.

At this point the project had established:

- Verified hardware inventory
- Complete data preservation
- Bootable installation media
- Deployment documentation
- Defined migration workflow

## Lessons Learned

Successful infrastructure deployments begin long before the operating system is installed. Establishing repeatable preparation procedures—including backups, hardware documentation, and validation of installation media—reduces deployment risk and provides a repeatable process for future system migrations.

## Next Steps

- Complete the Ubuntu Server installation.
- Configure the initial network connection.
- Enable remote administration through SSH.
- Begin building the infrastructure platform.
