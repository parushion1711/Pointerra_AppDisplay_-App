<template>
  <div> {{ messageData }} </div>
</template>

<script>

export default {
  name: 'DisplayBox',
  props: ['message'],
  data () {
    return {
      messageData: ''
    }
  },
  methods: {
    replaceAt (string, start, end, replacement) { // replace strings by index
      return string.substr(0, start) + replacement + string.substr(end)
    },
    wait (ms) {
      return new Promise((resolve) => setTimeout(resolve, ms))
    },
    async fetchData (link) { // returns data from fetched link
      const res = await fetch(
        link
      )
      if (res.ok) {
        return await res.text()
      }
      return 'Invalid Link'
    },
    findWhiteSpace (string, start) {
      const whiteSpace = [' ', '\t', '\n']
      let firstIndexWS = -1
      for (const whiteChar of whiteSpace) {
        const whiteSpaceIndex = string.indexOf(whiteChar, start)
        if (firstIndexWS === -1) { // checks if previous whiteChar was found
          firstIndexWS = whiteSpaceIndex
        } else if (whiteSpaceIndex < firstIndexWS && whiteSpaceIndex !== -1) { // finds min whiteChar index
          firstIndexWS = whiteSpaceIndex
        }
      }
      return firstIndexWS
    },
    async checker () {
      const link = 'http'
      let newMessage = this.message
      let position = 0
      const promises = []
      const indexList = []
      /* eslint-disable-next-line */
      while (true) {
        if (newMessage.indexOf(link, position) > -1) { // checks whether word is a link
          const linkStartIndex = newMessage.indexOf(link, position)
          const whiteSpace = this.findWhiteSpace(newMessage, linkStartIndex)
          const linkEndIndex = whiteSpace
          const addressLink = linkEndIndex > -1 ? newMessage.slice(linkStartIndex, linkEndIndex) : newMessage.slice(linkStartIndex)
          // await this.wait(1000)
          /* eslint-disable-next-line */
          const regex = new RegExp('^(http|https)://.+', 'i') // create regex for strings that start wtih http or https ://
          const match = regex.test(addressLink) // checks whether link matches regex (returns a boolean)
          if (match) {
            // const newWord = await this.fetchData(addressLink)
            indexList.push(newMessage.indexOf(addressLink)) // start index of 'Loading...'
            newMessage = newMessage.replace(addressLink, 'Loading...')
            promises.push(this.fetchData(addressLink))
          } else {
            position = newMessage.indexOf(link) + 1
          }
        } else break
      }
      this.messageData = newMessage
      const results = await Promise.allSettled(promises)
      let indexAdjustor = 0
      for (let i = 0; i < promises.length; i++) {
        const startReplaceIndex = indexList[i] + indexAdjustor
        const endReplaceIndex = startReplaceIndex + 10
        if (results[i].status === 'fulfilled') { // if link was valid
          newMessage = this.replaceAt(newMessage, startReplaceIndex, endReplaceIndex, results[i].value)
          indexAdjustor += results[i].value.length - 10
        } else { // if link was invalid
          newMessage = this.replaceAt(newMessage, startReplaceIndex, endReplaceIndex, 'Invalid Link')
          indexAdjustor += 2
        }
      }
      this.messageData = newMessage
    }
  },
  watch: {
    async message () {
      await this.checker()
    }
  }
}
</script>
