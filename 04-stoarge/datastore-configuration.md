# Datastore Configuration (Samsung EVO SSD)

## Goal
Configure the Samsung EVO SATA SSD as the **primary VM datastore** for this ESXi 7.0U3 host.

---

## Steps (ESXi Web UI)

1. Log into ESXi Host Client (Web UI)
2. Navigate to: **Storage → Devices**
3. Confirm the Samsung EVO SSD is visible and correctly identified by size/model.

### (Optional) Clear Existing Partitions
If the SSD shows old partitions (Windows leftovers):
- Select the SSD → choose **Erase/Clear/Delete Partitions** (wording depends on UI)
- This ensures a clean VMFS format.

---

## Create the Datastore (VMFS6)

1. Navigate to: **Storage → Datastores**
2. Click **New datastore**
3. Select **Create new VMFS datastore**
4. Choose the Samsung EVO SSD as the backing device
5. Select **VMFS 6**
6. Datastore name: `datastore-ssd01`
7. Choose **Use full disk**
8. Finish

---

## Verification

- Go to **Storage → Datastores**
- Confirm `datastore-ssd01` exists and shows expected capacity
- Click the datastore → confirm:
  - **Type:** VMFS
  - **Backing device:** Samsung EVO SSD