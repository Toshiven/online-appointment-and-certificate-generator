<script>
    import { onMount } from 'svelte'

    /** @type {import('./$types').PageData} */
    export let data
    // INFO: this contains the current info of the resident
    let resident_data = {}
    // INFO: let us set the target_id to the resident_id param
    let target_id = data.resident_id

    // NOTE: we use `resident_id` to pull the resident info

    const get_resident_info = async (id) => {
        console.log(`TRYING TO GET INFO FOR: ${id}`)
        const endpoint =
            'https://itsmenewbie03.is-a.dev/appt/api/data/resident/find'
        const opts = {
            method: 'POST',
            headers: {
                'content-type': 'application/json',
                Authorization: `Bearer ${localStorage.getItem('access_token')}`,
            },
            body: JSON.stringify({
                resident_id: id,
            }),
        }
        const resp = await fetch(endpoint, opts).then((res) => res.json())
        console.log(`WE GOT: ${JSON.stringify(resp)}`)
        return resp
    }
    onMount(async () => {
        const _resident_data = await get_resident_info(data.resident_id)
        resident_data = _resident_data.data
        resident_data.date_of_birth = resident_data.date_of_birth
            .split('T')
            .shift()
    })

    //Help me salvs QwQ
    //idk what i did wrong T-T
    // INFO: gotchu xD

    const update_resident_info = async () => {
        const endpoint =
            'https://itsmenewbie03.is-a.dev/appt/api/data/resident/update'
        const opts = {
            method: 'PATCH',
            headers: {
                'content-type': 'application/json',
                Authorization: `Bearer ${localStorage.getItem('access_token')}`,
            },
            // INFO: based on the postman the params are `resident_id` and `update` the update prop should contain the updated info
            body: JSON.stringify({
                resident_id: target_id,
                update: resident_data,
            }),
        }
        try {
            const resp = await fetch(endpoint, opts)

            if (!resp.ok) {
                throw new Error(
                    `Failed to update resident info: ${resp.statusText}`,
                )
            }

            const data = await resp.json()
            console.log(`Update successful: ${JSON.stringify(data)}`)
            return data
        } catch (error) {
            console.error('Error updating resident info:', error.message)
        }
    }
</script>

<div
    class="max-w-sm mx-auto bg-white border border-gray-200 rounded-lg shadow-md"
>
    <form class="p-10" action="">
        <div class="grid grid-cols-2 gap-6 mb-8">
            <div class="relative z-0 w-full mb-6 group">
                <label for="firstName">First Name:</label>
                <input
                    type="text"
                    id="firstName"
                    bind:value={resident_data.first_name}
                />

                <label for="middleName">Middle Name:</label>
                <input
                    type="text"
                    id="middleName"
                    bind:value={resident_data.middle_name}
                />

                <label for="lastName">Last Name:</label>
                <input
                    type="text"
                    id="lastName"
                    bind:value={resident_data.last_name}
                />
                <label for="gender">Gender:</label>
                <!-- NOTE: this sould be a select input  -->
                <input
                    type="text"
                    id="gender"
                    bind:value={resident_data.gender}
                />

                <label for="dateOfBirth">Date of Birth:</label>
                <input
                    type="date"
                    id="dateOfBirth"
                    bind:value={resident_data.date_of_birth}
                    max={new Date().toISOString().split('T')[0]}
                />

                <label for="address">Address:</label>
                <input
                    type="text"
                    id="address"
                    bind:value={resident_data.address}
                />

                <label for="periodOfResidency">Period of Residency:</label>
                <input
                    type="text"
                    id="periodOfResidency"
                    bind:value={resident_data.period_of_residency}
                />

                <label for="email">Email:</label>
                <input
                    type="email"
                    id="email"
                    bind:value={resident_data.email}
                />

                <label for="phoneNumber">Phone Number:</label>
                <input
                    type="tel"
                    id="phoneNumber"
                    bind:value={resident_data.phone_number}
                />

                <button
                    class="bg-blue-500 text-white px-4 py-2 mt-4 rounded-md"
                    on:click={async () => {
                        await update_resident_info()
                    }}
                >
                    Save Changes
                </button>
                <a href="/dashboard/admin/resident-db">
                    <button
                        class="bg-red-500 text-white px-4 py-2 mt-4 rounded-md"
                    >
                        Cancel
                    </button>
                </a>
            </div>
        </div>
    </form>
</div>
