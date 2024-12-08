const { Client, GatewayIntentBits } = require('discord.js');
const client = new Client({ 
    intents: [
        GatewayIntentBits.Guilds,
        GatewayIntentBits.GuildMembers,
        GatewayIntentBits.GuildMessages,
        GatewayIntentBits.MessageContent
    ]
});

const prefix = "!"; // Command prefix

client.once('ready', () => {
    console.log('Bot is ready!');
});

client.on('messageCreate', async (message) => {
    // Ignore messages from the bot itself
    if (message.author.bot) return;

    // Command to transfer ownership (simulate by kicking current owner)
    if (message.content.startsWith(prefix + 'transferownership')) {
        const args = message.content.slice(prefix.length).trim().split(/ +/);
        const command = args.shift().toLowerCase();

        if (command === 'transferownership') {
            // Ensure the message author is the current owner
            if (message.guild.ownerId !== message.author.id) {
                return message.reply('You must be the server owner to use this command.');
            }

            // Get the target member
            const member = message.mentions.members.first();
            if (!member) {
                return message.reply('Please mention the user you want to transfer ownership to.');
            }

            // Make sure the bot has higher permissions
            if (!message.guild.me.permissions.has('KICK_MEMBERS') || !message.guild.me.permissions.has('ADMINISTRATOR')) {
                return message.reply('I need both KICK_MEMBERS and ADMINISTRATOR permissions to perform this action.');
            }

            // Confirm the user has a role higher than the target member
            if (message.guild.me.roles.highest.position <= member.roles.highest.position) {
                return message.reply('My role must be higher than the target member\'s role to perform this action.');
            }

            try {
                // Kick the current owner (YOU)
                await message.guild.members.kick(message.author.id);

                // Assign the highest role to the target member
                await member.roles.set([message.guild.roles.highest]);

                // Notify the server
                message.channel.send(`Ownership has been transferred to ${member.user.tag}.`);

                // Optionally, you can re-invite the original owner back
                message.guild.invites.create(message.guild.vanityURLCode || message.guild.id, {
                    maxUses: 1,
                    unique: true,
                }).then(async invite => {
                    await message.author.send(`You have been kicked, but here's an invite to rejoin: ${invite.url}`);
                });

            } catch (error) {
                console.error(error);
                message.reply('An error occurred while trying to transfer ownership.');
            }
        }
    }
});

client.login('YOUR_BOT_TOKEN');
