<template>
  <div class="device">
    <h1>Управление устройствами</h1>

    <div class="device__form">
      <input v-model="newDeviceName" placeholder="Добавить устройство" />
      <button @click="addDevice">Добавить</button>
    </div>

    <ul class="device__list">
      <li v-for="device in devices" :key="device.name" class="device__item">
        <div v-if="editedDevice !== device" class="device__display">
          <span class="device__name">{{ device.name }}</span>
          <div class="device__actions">
            <button @click="editDevice(device)">Редактировать</button>
            <button @click="deleteDevice(device)">Удалить</button>
            <button @click="toggleNodes(device)">Узлы</button>
          </div>
        </div>

        <div v-else class="device__edit">
          <input v-model="editedDeviceName" />
          <button @click="saveDevice">Сохранить</button>
        </div>

        <div v-if="device.showNodes" class="device__node">
          <h3>Узлы</h3>
          <div class="device__node-form">
            <input v-model="newNodeName" placeholder="Добавить узел" />
            <button @click="addNode(device)">Добавить узел</button>
          </div>

          <ul class="device__node-list">
            <li
              v-for="(node, index) in device.nodes"
              :key="node.name"
              class="device__node-item"
            >
              <div v-if="editedNode !== node" class="device__node-display">
                <span class="device__node-name">{{ node.name }}</span>
                <div class="device__node-actions">
                  <button @click="editNode(node)">Редактировать</button>
                  <button @click="deleteNode(device, node)">Удалить</button>
                  <button
                    @click="moveNodeUp(device, index)"
                    :disabled="index === 0"
                  >
                    Вверх
                  </button>
                  <button
                    @click="moveNodeDown(device, index)"
                    :disabled="index === device.nodes.length - 1"
                  >
                    Вниз
                  </button>
                </div>
              </div>
              <div v-else class="device__node-edit">
                <input v-model="editedNodeName" />
                <button @click="saveNode">Сохранить</button>
              </div>
            </li>
          </ul>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      devices: [],
      newDeviceName: '',
      editedDevice: null,
      editedDeviceName: '',
      newNodeName: '',
      editedNode: null,
      editedNodeName: '',
    };
  },
  methods: {
    addDevice() {
      if (this.newDeviceName) {
        this.devices.push({
          name: this.newDeviceName,
          nodes: [],
          showNodes: false,
        });
        this.newDeviceName = '';
      }
    },
    editDevice(device) {
      this.editedDevice = device;
      this.editedDeviceName = device.name;
    },
    saveDevice() {
      if (this.editedDevice) {
        this.editedDevice.name = this.editedDeviceName;
        this.editedDevice = null;
        this.editedDeviceName = '';
      }
    },
    deleteDevice(device) {
      this.devices = this.devices.filter((d) => d !== device);
    },
    toggleNodes(device) {
      device.showNodes = !device.showNodes;
    },
    addNode(device) {
      if (this.newNodeName) {
        device.nodes.push({ name: this.newNodeName });
        this.newNodeName = '';
      }
    },
    editNode(node) {
      this.editedNode = node;
      this.editedNodeName = node.name;
    },
    saveNode() {
      if (this.editedNode) {
        this.editedNode.name = this.editedNodeName;
        this.editedNode = null;
        this.editedNodeName = '';
      }
    },
    deleteNode(device, node) {
      device.nodes = device.nodes.filter((n) => n !== node);
    },
    moveNodeUp(device, index) {
      if (index > 0) {
        const nodes = [...device.nodes];
        [nodes[index - 1], nodes[index]] = [nodes[index], nodes[index - 1]];
        device.nodes = nodes;
      }
    },
    moveNodeDown(device, index) {
      if (index < device.nodes.length - 1) {
        const nodes = [...device.nodes];
        [nodes[index + 1], nodes[index]] = [nodes[index], nodes[index + 1]];
        device.nodes = nodes;
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.device {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  color: #333;

  h1 {
    text-align: center;
    margin-bottom: 20px;
    color: #555;
  }

  &__form,
  &__node-form {
    display: flex;
    gap: 10px;
    margin-bottom: 15px;
  }

  input {
    flex: 1;
    padding: 8px;
    border: 1px solid #ccc;
    border-radius: 4px;
  }

  button {
    padding: 8px 12px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    background-color: #007bff;
    color: white;

    &:disabled {
      background-color: #aaa;
      cursor: not-allowed;
    }
    &:hover {
      opacity: 0.7;
    }
  }

  &__list,
  &__node-list {
    list-style: none;
    padding: 0;
  }

  &__node-item {
    border: 1px solid #e0e0e0;
    border-radius: 4px;
    padding: 10px;
    margin-bottom: 10px;
    background: #f9f9f9;
  }

  &__display,
  &__node-display {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  &__name,
  &__node-name {
    font-weight: bold;
  }

  &__actions,
  &__node-actions {
    display: flex;
    gap: 5px;

    button {
      padding: 4px 8px;
      border: none;
      border-radius: 4px;
      cursor: pointer;

      &:hover {
        opacity: 0.7;
      }
    }
  }

  &__edit,
  &__node-edit {
    display: flex;
    gap: 10px;

    button {
      padding: 8px 12px;
      border: none;
      border-radius: 4px;
      cursor: pointer;
      background-color: #28a745;
      color: white;

      &:hover {
        opacity: 0.7;
      }
    }
  }

  &__node {
    h3 {
      font-size: 1.1rem;
      margin-top: 15px;
      color: #555;
    }
  }
}
</style>
