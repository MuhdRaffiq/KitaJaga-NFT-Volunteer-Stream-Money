<template>
  <!-- :class="[
      !account && 'h-[40%] lg:h-screen rounded-t-terato-2xl lg:rounded-none'
    ]" -->
  <!-- <div
    v-if="account"
    class="wallet bottom-0 shadow-3xl bg-white
      lg:right-0 lg:h-screen lg:w-[40%] lg:max-w-[25vw]"
  >
    <AppWallet class="w-full" />
  </div> -->
  <div
    v-if="account"
    class="wallet bg-black bg-opacity-50"
  >
    <div class="m-auto max-w-md bg-white rounded-terato-2xl shadow-terato-2xl" v-if="!hasBecomeVolunteers || step_1 != 2">
      <div class="flex justify-end px-4 pt-4">
        <UiSystemButton @click.native="close" size="lg" icon="close" is-light />
      </div>
      <div class="p-4">
        <!-- <UiWalletConnectWallet class="" /> -->
        <div class="grid gap-4" v-if="step_1 == 0 && step_2 == 0">
          <ui-system-button class="w-full" @click.native="step(1,1)">
            Donate Crypto to KitaJaga Volunteers
          </ui-system-button>
          <ui-system-button class="w-full" @click.native="onBecomeVolunteers" v-bind:isDisabled="!isLoaded">
            {{isLoaded ? "Be KitaJaga Volunteers" : "Loading..."}}
          </ui-system-button>
        </div>

        <!-- [Start] Donate Crypto to KitaJaga Volunteers -->
        <div class="grid gap-4" v-if="step_1 == 1 && step_2 == 1">
          <div>
            <div class="font-bold">
              Choose the campaign that you wanted to donate
            </div>
            <p>
              Here is where the users can donate their crypto to KitaJaga
              donation pool that will be distributted to the volunteers
            </p>
          </div>
          <ui-system-button class="w-full" @click.native="step(1, 2)"
            >Flood Victims</ui-system-button
          >
          <ui-system-button class="w-full" @click.native="step(1, 2)"
            >MCO Lockdown</ui-system-button
          >
        </div>

        <div class="grid gap-4" v-else-if="step_1 == 1 && step_2 == 2">
          <div>
            <div class="font-bold">Choose causes that you wanted to donate</div>
            <p>
              End of Demo<br />Here user will donate some crypto to the<br />selected
              campaign<br />Future iteration using superfluid to send money
            </p>
          </div>
          <ui-system-button class="w-full" @click.native="step(0, 0)"
            >Go Back</ui-system-button
          >
        </div>
        <!-- [End] Donate Crypto to KitaJaga Volunteers -->

        <!-- [Start] Be KitaJaga Volunteers -->
        <div class="grid gap-4" v-if="step_1 == 2 && step_2 == 1">
          <div class="flex flex-col gap-y-3">
            <div class="font-bold">
              Be KitaJaga Volunteers by minting KitaJaga NFT and gain
              access to stream of money
            </div>
            <p>
              This is volunteer membership NFT. YOU are entrusted  with trust to
              help people on the ground. Stream of money (100 USD / Month).
              Choose which causes you want to volunteer.
              Click button down here to mint NFT
            </p>
            <p>
              I want to help...
            </p>
          </div>
          <ui-system-button class="w-full"
            @click.native="onMintNftForVolunteers('floodVictims')"
            v-bind:isDisabled="volunteers.isMinting"
          >
            {{volunteers.floodVictimsButton.label}}
          </ui-system-button>
          <ui-system-button class="w-full"
            @click.native="onMintNftForVolunteers('mcoLockdown')"
            v-bind:isDisabled="volunteers.isMinting"
          >
            {{volunteers.mcoLockdownButton.label}}
          </ui-system-button>
        </div>

        <div class="grid gap-4" v-else-if="step_1 == 2 && step_2 == 2">
          <div class="flex flex-col gap-y-3">
            <!-- <div class="font-bold">Choose causes that you wanted to donate</div> -->
            <p>NFT has been minted</p>
            <p>Go to this address to see you NFT in Opensea!</p>
            <a
              href="https://testnets.opensea.io/collection/kitajaga-volunteer-flood-victims-nft"
              target="_blank"
              class="underline text-blue-500"
            >
              KitaJaga Volunteer Flood Victims NFT
            </a>
          </div>
          <ui-system-button class="w-full" @click.native="step(0,0)">Go Back</ui-system-button>
        </div>
        <!-- [End] Be KitaJaga Volunteers -->
      </div>
    </div>

    <!-- [Start] You NFT -->
    <div
      class="
        wallet absolute w-full h-full inset-y-0 right-0 shadow-3xl bg-white
        sm:w-1/2 md:w-1/3 lg:h-screen lg:w-1/4 lg:max-w-[25vw] overflow-y-auto
      "
      v-else
    >
      <!-- Header -->
      <div class="flex p-4 items-center gap-x-3 sticky top-0 backdrop-filter backdrop-blur">
        <NuxtLink
          to="/map"
          class="flex items-center justify-center w-12 h-12 p-2 transition
          bg-white rounded-full focus:outline-none shadow-kj-gray-xs hover:bg-gray-200"
        >
          <img src="~assets/img/arrow-down.svg" alt="" />
        </NuxtLink>

        <div class="flex flex-col">
          <h2
            class="text-lg font-bold capitalize"
          >
            KitaJaga Volunteer
          </h2>
        </div>
      </div>
      <!-- Content -->
      <div class="p-4 pb-10 space-y-4">
        <div class="">
          <h2 class="font-bold text-xl">You NFT</h2>
          <img
            v-for="metadata in volunteers.metadatas"
            v-bind:key="metadata.id.toString()"
            :src="metadata.image" :alt="metadata.name"
          />
        </div>
        <div class="flex flex-col gap-y-3">
          <div>
            <h2 class="font-bold text-xl">You Balance</h2>
            <div>USDC: {{volunteers.usdcBalance}}</div>
          </div>
          <div>
            <h3 class="font-semibold text-lg">You received from Donation</h3>
            <div>USDCx: {{volunteers.usdcxBalance}}</div>
          </div>
          <a
            href="https://app.superfluid.finance/currencies"
            target="_blank"
          >
            <UiSystemButton>convert USDCx to USDc</UiSystemButton>
          </a>
          <div>Stream rate: {{volunteers.streamRate}} per second</div>
        </div>
      </div>
    </div>
    <!-- [End] You NFT -->

  </div>
  <!-- flex justify-center items-center overflow-y-scroll -->
  <div v-else class="wallet bg-black bg-opacity-50">
    <div class="m-auto max-w-md bg-white rounded-terato-2xl shadow-terato-2xl">
      <div class="flex justify-end px-4 pt-4">
        <UiSystemButton @click.native="close" size="lg" icon="close" is-light />
      </div>
      <div class="p-4">
        <UiWalletConnectWallet class="" />
      </div>
    </div>
  </div>
</template>

<script>
import { ethers } from 'ethers';
import { mapState, mapActions } from 'vuex'

import KITAJAGA_SUPERFLUID_ABI from '../../../config/abis/superfluid.json'
import ERC20_ABI from '../../../config/abis/erc20.json'

const kjSuperfluidAddress = "0xFf07A0466dD613334DA91Cd0E7A94DD3e69ff9B3"
const usdcAddress = "0xbe49ac1EadAc65dccf204D4Df81d650B50122aB2"
const usdcxAddress = "0x0F1D7C55A2B133E000eA10EeC03c774e0d6796e8"

export default {
  props: {
    isMobile: {
      type: Boolean,
      default: false
    }
  },
  layout(context) {
    if (context.env.TOGGLE_MAINTENANCE_MODE == "on") {
      return "default-maintenance";
    } else if (context.env.TOGGLE_MAINTENANCE_PKP == "on") {
      return "default-pkp";
    } else {
      return "default";
    }
  },
  data() {
    return {
      isLoaded: false,
      removeEvent: () => {},
      step_1: 0,
      step_2: 0,
      hasBecomeVolunteers: false,
      volunteers: {
        isMinting: false,
        floodVictimsButton: {
          label: 'Flood Victims'
        },
        mcoLockdownButton: {
          label: 'MCO Lookdown'
        },
        metadatas: [],
        eventLogs: [],
        usdcBalance: "",
        usdcxBalance: "",
        streamRate: ""
      },
      interval: ""
    }
  },
  async created() {
    this.ipfs = this.$ipfs()
  },
  computed: {
    ...mapState("web3", ["isActive", "account", "provider"])
  },
  async mounted() {
    await this.activate();
    this.removeEvent = await this.addEvent();
    // document.addEventListener('click', this.closeWhenClickedOutside);
    this.$nuxt.$on('close-modal', this.close)

    console.log("is loaded", this.isLoaded)
    if (this.account) {
      await this.checkHasBecomeVolunteers()
      await this.getUserVolunteersNft()
      await this.getBalance()
    }

    this.isLoaded = true
  },
  beforeDestroy() {
    this.removeEvent();
    clearInterval(this.interval)
    // document.removeEventListener('click', this.closeWhenClickedOutside);
  },
  watch: {
    async account() {
      if (this.account) {
        this.isLoaded = false
        await this.checkHasBecomeVolunteers()
        await this.getUserVolunteersNft()
        await this.getBalance()
        this.isLoaded = true
      }
    }
  },
  methods: {
    ...mapActions("web3", ["activate", "connectWallet", "addEvent"]),
    close() {
      // document.removeEventListener('click', this.closeWhenClickedOutside);
      this.$router.push("/map");
    },
    closeWhenClickedOutside(event) {
      if (!event.target.closest(".wallet")) {
        this.close();
      }
    },
    step(step1, step2) {
      this.step_1 = step1;
      this.step_2 = step2;
    },
    async queryUserVolunteersNft() {
      console.log("Query user volunteers NFT")
      const contract = new ethers.Contract(
        kjSuperfluidAddress, KITAJAGA_SUPERFLUID_ABI, this.provider()
      )
      const userNftEventFilter = contract.filters.NFTIssued(null, this.account)
      console.log("user NFT event filter", userNftEventFilter)
      const userNftEvents = await contract.queryFilter(userNftEventFilter, 0, 'latest')
      console.log("User volunteers NFT logs", userNftEvents)
      this.volunteers.eventLogs = userNftEvents
      return userNftEvents
    },
    async getUserVolunteersNft() {
      const contract = new ethers.Contract(
        kjSuperfluidAddress, KITAJAGA_SUPERFLUID_ABI, this.provider()
      )
      const events = await this.queryUserVolunteersNft()
      const tokenIds = events.map(e => e.args.tokenId)
      const tokenMetadataPromises = tokenIds.map(async id => {
        console.log("id", id)
        const tokenUri = await contract.tokenURI(id)
        console.log("tokenUri", tokenUri)
        const metadata = await this.ipfs.getJSON(
          tokenUri.replace("ipfs://", "")
        )
        console.log("metadata", metadata)
        const imageUri = await this.$ipfs().getImageUri(
          metadata.image.replace("ipfs://", "")
        )
        console.log("iamgeUri", imageUri)
        return { id, ...metadata, image: imageUri }
      })
      const tokenMetadatas = await Promise.all(
        tokenMetadataPromises.map(p => p.catch(() => undefined))
      )
      console.log("User volunteer NFT metadata", tokenMetadatas)
      this.volunteers.metadatas = tokenMetadatas
    },
    async getBalance() {
      const provider = this.provider()
      const usdcContract = new ethers.Contract(usdcAddress, ERC20_ABI, provider)
      const usdcBalance = await usdcContract.balanceOf(this.account)
      const usdcDecimals = await usdcContract.decimals()
      this.volunteers.usdcBalance = ethers.utils.formatUnits(usdcBalance, usdcDecimals)
      console.log("usdc balance", usdcBalance)


      const usdcxContract = new ethers.Contract(usdcxAddress, ERC20_ABI, provider)
      const usdcxDecimals = await usdcContract.decimals()

      const arrayTest = []
      arrayTest.reduce((prev, curr) => prev.add(curr), ethers.constants.Zero)
      const flowRates = this.volunteers.eventLogs.map(log => log.args.flowRate)
      const totalFlowRate = flowRates.reduce((prev, curr) => prev.add(curr), ethers.constants.Zero)
      const totalFlowRateFormatted = ethers.utils.formatUnits(totalFlowRate, usdcxDecimals)
      this.volunteers.streamRate = totalFlowRateFormatted
      console.log(" total flow rate", { totalFlowRate, totalFlowRateFormatted })

      const usdcxBalance = await usdcxContract.balanceOf(this.account)
      this.volunteers.usdcxBalance = ethers.utils.formatUnits(usdcxBalance, usdcxDecimals)
      console.log("usdcx balance", usdcxBalance)
      this.interval = setInterval(async () => {
        const currBalance = ethers.utils.parseUnits(this.volunteers.usdcxBalance, usdcxDecimals)
        const newBalance = currBalance.add(totalFlowRate)
        this.volunteers.usdcxBalance = ethers.utils.formatUnits(newBalance, usdcxDecimals)
      }, 1000)
    },
    async checkHasBecomeVolunteers() {
      const events = await this.queryUserVolunteersNft()
      const hasBecomeVolunteers = events.length > 0
      this.hasBecomeVolunteers = hasBecomeVolunteers
    },
    async onBecomeVolunteers() {
      if (this.hasBecomeVolunteers) {
        this.$notify({
          text: "You already become KitaJaga Volunteer",
          type: "info",
          duration: 5000,
          data: {
            emoji: "😉"
          }
        })
        this.step(2, 2)
      }
      else this.step(2, 1)
    },
    async onMintNftForVolunteers(category) {
      this.volunteers.isMinting = true
      switch (category) {
        case 'floodVictims':
          this.volunteers.floodVictimsButton.label = 'Minting...'
          break;
        case 'mcoLockdown':
          this.volunteers.mcoLockdownButton.label = 'Minting...'
          break;
        default:
          throw new Error('Volunteers category not found.')
      }
      try {
        const tx = await this.mintNft()
        await tx.wait()
        this.step(2, 2)
      } catch (error) {
        console.error("cannot mint nft")
        this.$notify({
          text: "Cannot mint NFT",
          type: "error",
          duration: 5000,
          speed: 500,
          data: {
            emoji: "😫"
          }
        })
        throw error
      } finally {
        this.isMinting = false
      }
    },
    async mintNft() {
      console.log("mint NFT KitaJaga")
      const contract = new ethers.Contract(
        kjSuperfluidAddress, KITAJAGA_SUPERFLUID_ABI, this.provider()
      )
      const signer = this.provider().getSigner()
      const contractWithSigner = contract.connect(signer)
      try {
        const tx = await contractWithSigner.issueNFT()
        return tx
      } catch (error) {
        console.error("could not issue NFT")
        throw error
      }
    }
  }
};
</script>
